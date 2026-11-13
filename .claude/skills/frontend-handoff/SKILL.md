---
name: frontend-handoff
description: Write the handshake folder the frontend team builds from - docs/frontend-api/<app>/ with flow.md (call order and who acts when), payloads.md (the exact request and response bodies) and walkthrough.md (the screen-by-screen story with real calls). Use when an endpoint, screen or workflow is built or changed, or when the frontend asks "which API do I call when".
---
# Frontend handshake (`docs/frontend-api/<app>/`)

The backend isn't done until the frontend can build without asking. This folder is **only** for the frontend team: one folder per backend app (same name as `backend/<app>/`), exactly three files.

```
docs/frontend-api/
  README.md            the contract + a list of every app and page
  shared/              login, pickers, errors, pagination envelope, websocket
  <app>/
    flow.md            which call, when, by whom
    payloads.md        exact bodies in and out
    walkthrough.md     the story of the screen, step by step
    <page>/            only for a big screen or a multi-person workflow (same three files)
```

**Not here:** business-logic memory, screen maps, design images, ER diagrams. Those are backend-only and live in `docs/backend/<app>/`. Use them as your source when writing these three files, but never copy internal rules or decisions into the handshake - the frontend gets what to call and what comes back, not why.

## flow.md - the call order
1. **Landing**: which calls render the page, which flags gate what (`can_create`, `editable`, `actions`).
2. **Each popup or action**: lookups first, then the submit call, then what happens after.
3. Endpoints in backticks with one line each: `` `POST /api/orders/assign/{id}/` `` - assign the order.

**Workflow between people**: a small who-hands-to-whom diagram on top, one section per person (`## Part 1 - the creator`), every part ending with a bold line: `**The creator's part ends here.** The reviewer has it (Part 2).` Say the backend decides branches and which answer field reports it.

## payloads.md - the contract
For every endpoint the screens use, one block:
```markdown
### POST /api/orders/assign/{id}/
Request
{ "assignee": 12, "due": "2026-12-01", "note": "..." }
Response 200
{ "id": 7, "reference": "ORD-0007", "status": "assigned", "message": "Assigned to Sara", "next": "review", "next_by": "Sara" }
Errors: 400 field errors · 403 not your turn · 409 already moved
```
- Real values, not `string`.
- Exactly what is sent and returned. If a field is here, the screen uses it.
- Table rows are flat. Buttons answer `{id, reference, status, message, next, next_by}`.

## walkthrough.md - the story
Walk a real user through the screen, numbered: what they see, what they click, which call fires, what the answer changes on screen. Include the refusals: 400 keep the popup and mark fields, 403 close and say it's not their turn, 409 close and reload.

## Rules
- Write for a frontend developer who has never seen the backend; use the screen's names.
- Tell them what to read off the answer (`actions`, `can_*`), never to decide by role or status text.
- Say plainly what isn't built yet.
- Field detail must match Apidog (`api-doc`). If they disagree, fix one of them before the PR.
- Commit these docs with the change they describe.
