---
name: screen-api-map
description: Build a canvas (one card per frontend screen) showing which APIs each screen needs, what each call takes and what it gives back - before writing the endpoints. Use at the start of a feature when designs exist, when asked "what APIs does this page need", or to check backend coverage against the design.
---
# Screen -> API map

Design first, endpoints second. Before building an app, map every screen of its design to the calls it needs. The map is the plan the backend is built from and the checklist it is reviewed against.

## 1. Read the design
Save each screen's image in `docs/backend/<app>/images/<screen>.png` and link it from its card.
For each screen (Figma frame, screenshot or description): what it shows (cards, table columns, counters), what the user can do (buttons, filters, forms, popups), and who sees it.

## 2. One card per screen
Write `docs/backend/<app>/screens.md` (or a canvas/FigJam board if the user has one), a card per screen:

```markdown
## Orders - list page
| Call | Takes | Gives |
|---|---|---|
| `GET /api/orders/page/summary/` | - | counters: open, late, done |
| `GET /api/orders/page/table/` | page, search, status, customer | rows: id, reference, customer, status, due |
| `GET /api/orders/options/customers/` | search | id, label |
| `POST /api/orders/create/` | customer, due, items[] | id, reference, status, message, next |
Buttons from: `can_create` (summary)

**Business logic**
- Who sees it: members with `orders.view_order`; others see only orders assigned to them.
- Late = `due` passed and status not `done` (computed by the backend, not the screen).
- Create: customer must be active; reference generated `ORD-0001`; status starts `draft`.
- After create: the assignee is notified (Celery task); the counters refresh.
- Open questions: can a done order be reopened? -> ask, then write the answer in `memory.md` -> Decisions (or `business.md` if it is a rule for the whole domain).
```

Every card has a **Business logic** block: who sees the screen, what each number means, the rules behind each button (validation, status moves, who acts next), side effects, and the open questions. A screen whose logic isn't written isn't ready to build - ask the user. The final rules also go into `docs/backend/<app>/memory.md` (`app-memory`).

Rules:
- **Takes** = exactly the inputs on the screen. **Gives** = exactly what is drawn. Nothing more.
- Pickers come from `shared-lookups`; lists use the shared pagination envelope.
- One popup = its own small block under the screen.

## 3. Check it
- Every visible value has a source; every input has a destination.
- No two screens call different endpoints for the same list.
- Mark what isn't built yet.

## 4. Keep it alive
The map is the baseline for `api-shared-layer` (what to return), `frontend-handoff` (payloads) and `pre-pr-check` (missing or extra data). Update it when the design changes.
