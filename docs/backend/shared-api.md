# Shared API index

Fast lookup for everything shared. **Search here before building any list, dropdown, filter or helper.** If it's here, reuse it. If you build a new shared one, add its row in the same PR.

## Shared tools (`backend/core/`)
| Need | Use | File |
|---|---|---|
| Paginated list | `paginate(request, qs, serialize)` | `core/pagination.py` |
| Table filters | `FilterSerializer` + `filter_queryset()` | `core/filters.py` |
| Cached read | `cached(namespace, parts, build)` + `bump(namespace)` | `core/cache.py` |
| 304 on unchanged | `etag_response(request, data)` | `core/cache.py` |
| Picker endpoint | `picker()` | `core/lookups.py` |

## Shared endpoints
| Endpoint | Gives | Used by | Owner app |
|---|---|---|---|
| `GET /api/core/health/` | `{status}` | monitoring | `core` |
| `POST /api/auth/login/` | `{access, refresh, user}` | login screen | `users` |
| `GET /api/auth/me/` | current user | every screen | `users` |
| _add a row per picker / form-options / meta_ | | | |
