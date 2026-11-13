---
name: pre-pr-check
description: The review before a PR - tests, then a contract review that reads the payloads against the design and the flow, checks every body and response for missing or extra data, and compares the ER diagram with the last version. Use when a change is "ready", before opening a PR, or when asked to review backend code.
---
# Pre-PR check (how I review)

Don't open the PR until every step is done or explicitly skipped with a reason.

## 1. Tests
`python manage.py test <changed apps>` (full suite if `core/`, `config/` or a shared signal changed). Fix failures; never weaken an assertion to pass.

## 2. Read the contract, not just the code
For each screen this change touches:
1. **Payloads vs design** - open `docs/frontend-api/<app>/payloads.md` next to the design (Figma or the screen map). Every field the screen shows must come from the response; every input on the form must be in the request.
2. **Payloads vs flow** - read `flow.md` and `walkthrough.md`. Each step calls an endpoint that exists, in that order, and each handoff ends where the doc says.
3. **Body and response, field by field**:
   - required data is there (ids for pickers, `can_*` / `actions` for buttons, `message` / `next` on actions)
   - **nothing extra**: no field the screen doesn't use, no nested objects in table rows, no internal ids or audit fields leaking
   - names match between request and response (`assignee` in, `assignee` out)
4. **ER diagram** - run `er-diagram` and compare with `docs/backend/<app>/erd.dbml`. The old design must still fit the new version: no orphaned columns, no duplicated fields, right `on_delete`.

## 3. Code review
Run `/code-review` on the diff, then `clean-code-review` and `security-check`. Fix or note each finding.

## 4. Docs
- `api-doc`: every new or changed endpoint published to Apidog.
- `frontend-handoff`: flow / payloads / walkthrough updated.
- `screen-api-map`: updated if a screen gained or lost a call.

## 5. Translation (last)
`translation` skill, then run the tests again.

## 6. Open the PR and record it
Open the PR. Then write `docs/pr-sessions/pr-<number>.md`: what changed, and one line per step above (passed / found X, fixed / skipped because Y).
