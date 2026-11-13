---
name: api-doc
description: Document every new or changed endpoint in one fixed format and publish it to Apidog (the source of truth) before the PR. Use whenever an endpoint's request or response changes.
---
# API docs -> Apidog

Apidog is the source of truth for endpoint detail. The repo holds the call order (`frontend-handoff`); Apidog holds the fields.

## 1. Write it in the same shape every time
For each endpoint, in this order:
1. `METHOD /path/`
2. Summary - one or two lines: what it's for, which screen calls it.
3. Auth & permission - which permission gates it (`permissions` skill).
4. Path params, query params, request body - tables: name, type, required, notes.
5. Responses - one row per status code it really returns.
6. Example request and response - real JSON.
7. Steps - what the view does end to end: validates, writes, side effects (signals, Celery tasks, websocket messages).

## 2. Publish
- Build an OpenAPI fragment for the endpoint (drf-spectacular's schema, or hand-written) and import it into the Apidog project through the Apidog import API or its MCP.
- The token lives in an environment variable (`APIDOG_ACCESS_TOKEN`), never in the repo. If it's missing, ask the user to set it - don't skip the step.
- Check in Apidog that the endpoint shows up in the right folder.

## 3. Rules
- Draft files (`<app>/doc/`, `<app>/apis/`) are working files - keep them gitignored.
- In the PR, name the Apidog folder + method + path instead of linking a file.
- The response documented must equal `payloads.md` and the serializer. Three places, one truth.
