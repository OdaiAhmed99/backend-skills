---
name: app-memory
description: Keep one memory.md per business app (docs/backend/<app>/memory.md) holding its business rules, decisions and gotchas, so any session knows the app's logic without re-reading all the code. Read it before touching an app; update it when a rule or decision changes.
---
# App memory

Code says *what* the app does. `docs/backend/<app>/memory.md` says *why*. It keeps the rules you'd otherwise lose between sessions.

`docs/backend/<app>/` is **backend only** - everything we know and decided about the app:
```
docs/backend/<app>/
  memory.md     business logic: rules, flow, decisions, gotchas, not built yet   <- this skill
  screens.md    each screen: calls + its business logic (screen-api-map)
  images/       the design screenshots / Figma exports the logic was discussed on
  erd.dbml      last approved ER diagram (er-diagram)
```
The frontend never reads this folder; their handshake is `docs/frontend-api/<app>/` (`frontend-handoff`). When you save a design image, name it after the screen (`list-page.png`) and link it from `screens.md`.

## Read first
Before changing anything in `backend/<app>/`, read `docs/backend/<app>/memory.md`. If the code and the memory disagree, stop and ask which is right.

## Shape (keep it short, one screen if possible)
```markdown
# orders - memory

## What it is
One line: what the app is for, who uses it.

## Rules
- An order starts `draft`; only the creator can submit it.
- Late = due passed and not `done` (backend computes it).

## Flow
draft -> submitted -> assigned -> done   (who moves each step)

## Decisions
- 2026-11-13: done orders can't be reopened - make a new one. (asked the user)

## Gotchas
- `assignee` is a membership id, not a user id.

## Not built yet
- Email reminders.
```

## Rules
- Rules and decisions only, no code copies and no field lists (those are in serializers and Apidog).
- Each decision has a date and where it came from.
- Rules from `screen-api-map` business logic land here once they're agreed.
- Update it in the same PR as the change. A new app gets a `docs/backend/<app>/memory.md` on day one (`build-app`).
- Nothing secret or customer-specific - this file is committed.
