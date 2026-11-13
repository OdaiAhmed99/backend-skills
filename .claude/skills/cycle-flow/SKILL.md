---
name: cycle-flow
description: The ordered backend cycle from "build this" to "PR open and recorded". Use when starting any feature, endpoint or app.
---
# Development cycle

```
business.md -> screen-api-map (+ business logic) -> app-memory -> build-app (+ api-shared-layer, shared-lookups, permissions)
  -> db-integrity -> er-diagram -> unit-test -> background-jobs / signals (if needed)
  -> api-doc -> frontend-handoff -> pre-pr-check -> translation -> PR -> PR record
```

1. **business.md** - the domain is answered, not guessed.
2. **screen-api-map** - every screen mapped to what it takes and gives, with its business logic written; agreed rules go to `docs/backend/<app>/memory.md` (`app-memory`).
3. **build-app** - model, serializer, service, view, url (serializer-first). Lists/filters/cache via `api-shared-layer`, dropdowns via `shared-lookups`, gates via `permissions`.
4. **db-integrity** + **er-diagram** - no duplicate fields, constraints in the DB, the diagram still fits.
5. **unit-test** - one test per decision, green.
6. **background-jobs / signals** - only if the work is slow, scheduled, or triggered by a save.
7. **api-doc** - published to Apidog.
8. **frontend-handoff** - flow, payloads, walkthrough.
9. **pre-pr-check** - the contract review, `/code-review`, `security-check`.
10. **Indexes** - `docs/backend/shared-api.md` for any new shared endpoint, `docs/backend/<app>/memory.md` for any changed rule.
11. **translation** - last, then tests again.
12. Open the PR and write `docs/pr-sessions/pr-<n>.md`.

Skip a step only explicitly ("no api-doc: Celery-only change"), never silently.
