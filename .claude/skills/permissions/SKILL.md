---
name: permissions
description: How access is decided - plain Django permissions on per-tenant roles, checked with user.has_perm(perm, tenant), with row scope in each model's visible_to() queryset. Use when adding or changing a permission check, a default role, can_* flags for the frontend, or when reviewing whether a view is permission-correct.
---
# Permissions

Plain Django permissions plus one small adapter. No rule engine, no registry, no role names in views. If you start writing one, stop.

```
Tenant -> Role -> django.contrib.auth Permission
           ^
  Membership.role   (one active role per user per tenant)
```

"Tenant" is whatever owns the data in `business.md`: a company, a project, a workspace.

| Question | Answered by |
|---|---|
| What actions exist? | Django `Permission` rows: `add_/change_/view_/delete_<model>` + `Meta.permissions` |
| What may a role do? | `Role.permissions` (M2M to `Permission`) |
| Which role does the user have here? | `Membership.role`, only when the membership is active |
| May the user do X here? | `request.user.has_perm("app.codename", tenant_or_record)` |
| Which rows may they see? | `Model.objects.visible_to(user, tenant)` |
| What does a new tenant start with? | `DEFAULT_ROLES` in `core/permissions/defaults.py` |
| What may the caller do (for the frontend)? | `GET /api/permissions/me/` |

## 1. Two calls, every time

**`user.has_perm(perm, obj)`** - a custom auth backend answers it. True only if an object is passed, a tenant can be read from it (`obj` or `obj.tenant`), the user is active, has an active membership there, and the role holds the permission.

- Always pass the tenant or a record. `has_perm(perm)` without an object is always False.
- Strings are `"<app_label>.<codename>"`. A typo silently denies, so a test checks every default permission exists.
- It answers at tenant level. Whether a row is "yours" is scope, below.

**`Model.objects.visible_to(user, tenant)`** - a queryset method that returns the rows this user may see, and `none()` when nothing grants access. Never fall back to all rows.

```python
class TaskQuerySet(TenantScopedQuerySet):
    def visible_to(self, user, tenant):
        rows = super().visible_to(user, tenant)           # tenant rows, or none() without view_task
        if user.has_perm("tasks.view_all_task", tenant):
            return rows                                    # sees everything
        return rows.filter(assigned_to__user=user)         # sees own/assigned only
```

- `view_<model>` = may see these records at all; `view_all_<model>` = sees every record, not only their own.
- Only models whose rows differ per user need `visible_to()`.
- Never decide scope by role name (`if role == "Manager"`). Give the role a permission.

## 2. Gating a view

Order: **tenant boundary -> permission -> load through scope -> validate -> write.**

Declare the gate, don't hand-write it:

```python
class TaskView(APIView):
    permission_classes = [TenantPermission]
    required_perms = {"GET": "tasks.view_task", "POST": "tasks.add_task"}
```

- A method with no entry in `required_perms` raises `ImproperlyConfigured`. A missing gate is a bug, not a deny.
- Another tenant's record, or a row outside scope -> **404**, not 403, so nobody learns it exists.
- Allowed to see but not to act -> **403** with a translated `message`.
- Checks that depend on the body or one record ("only the assignee") stay explicit, after the gate.
- Cache `has_perm` per request; clear the cache if you change a role inside the same request.

## 3. Tell the frontend, don't make it guess

Detail and page endpoints return flags computed from `has_perm` - `can_create`, `can_edit`, `actions: [...]`. The frontend shows buttons from these flags and never decides by role or status text.

## 4. Adding a permission or a role

1. Add it to `Meta.permissions` -> `makemigrations`.
2. Add it to the roles that need it in `DEFAULT_ROLES`.
3. Write a data migration or command so existing tenants get it too.
4. Tests: allowed role -> 200, other role -> 403, other tenant -> 404, out-of-scope row -> 404.

## Checklist
- [ ] Every method has a declared permission
- [ ] Lists and detail load through `visible_to()`
- [ ] No role names or status strings deciding access
- [ ] `can_*` / `actions` returned for the screen
- [ ] The four tests above exist and pass
