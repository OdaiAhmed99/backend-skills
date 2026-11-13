# backend-skills - project template

A backend-only template and skill set for developers: a **Django REST** API with **Postgres + Redis**, **Celery** for background work and **Channels** for websockets. The frontend is another team; this backend hands them a clear contract.

## Layout

```
backend-skills/
  CLAUDE.md               # this file - read every session
  business.md             # the domain: roles, records, rules - asked at project start
  docker-compose.yml      # db, redis, backend, celery, beat, nginx
  nginx/default.conf      # the only public entry: /api /admin /ws /static /media (nginx skill)
  .env.example            # copy to .env (never commit .env)
  backend/                # Django project: config/ + core/ + users/ + one folder per app
  docs/
    frontend-api/         # FOR THE FRONTEND TEAM - the handshake, nothing else (frontend-handoff)
      README.md           # the contract + list of apps and pages
      shared/             # login, pickers, errors, pagination envelope, websocket
      <app>/
        flow.md           # call order, who acts when
        payloads.md       # exact request / response bodies
        walkthrough.md    # the story, step by step
    backend/              # FOR THE BACKEND ONLY
      shared-api.md       # fast lookup: every shared tool and endpoint - search before building
      <app>/              # what we know and decided about the app
        memory.md         # business logic: rules, flow, decisions, gotchas, not built yet (app-memory)
        screens.md        # each screen: calls (takes / gives) + its business logic (screen-api-map)
        images/           # design screenshots / Figma exports the logic was discussed on
        erd.dbml          # last approved ER diagram (er-diagram)
    pr-sessions/          # one record per PR (pre-pr-check)
  .claude/skills/         # how we work (below)
```

## Run

```bash
docker compose up -d --build                       # Postgres, Redis, backend, celery, beat, nginx on :80
docker compose exec backend python manage.py createsuperuser
```
API docs at http://localhost/api/docs/. Without Docker: create a venv, `pip install -r backend/requirements.txt`, `python backend/manage.py migrate && python backend/manage.py runserver` (SQLite, Celery runs inline).

## Start here: the business

**Before building anything, read [business.md](business.md).** If it still has unanswered questions (`_answer:_` lines), ask the user - a few at a time, in plain words - and write the answers in. Don't guess the domain.

## Principles

- **Design first**: no endpoint without a screen that needs it (`screen-api-map`).
- **Serializer-first**: views validate and shape through serializers; business logic in `services/` on `validated_data`.
- **One shared layer**: pagination, filters, cache and lookups come from `core/`; never copied.
- **Look before you build**: search `docs/backend/shared-api.md` and `docs/backend/<app>/memory.md` first.
- **Only what the screen uses**: table rows flat, popups answer their screen, buttons answer `{id, reference, status, message, next, next_by}`.
- **Permissions**: authenticated -> tenant boundary (404) -> `has_perm(perm, tenant)` -> `visible_to()` scope. Default deny. Never by role name.
- **The contract is part of the code**: Apidog + `docs/frontend-api/<app>/` are updated in the same PR.
- **I lead, Claude builds**: every file stays readable by a human. If a change can't be explained in the PR record, it isn't ready.

## App registry

| App | URL prefix | Purpose |
| --- | --- | --- |
| `core` | `/api/core/` | Shared plumbing: pagination, filters, cache + ETag, lookups, health. |
| `users` | `/api/auth/` | Custom `User`; JWT login with username or email, refresh, me. |

Every new app: in `INSTALLED_APPS`, wired in `config/urls.py` under `/api/`, has `tests/`, and a row here.

## Skills

| Skill | Use it when |
|---|---|
| `cycle-flow` | starting any feature - the order of everything |
| `screen-api-map` | a design exists - map each screen to what it takes and gives |
| `build-app` | app shape: view / serializer / service / url |
| `api-shared-layer` | any list, filter, cache or serializer |
| `api-shared-layer` vs `shared-lookups` | the layer = how every endpoint is paginated, filtered, cached, shaped; lookups = where each dropdown list lives (once) |
| `shared-lookups` | any dropdown, picker, form options |
| `app-memory` | before touching an app: read `docs/backend/<app>/memory.md`; update it when a rule changes |
| `nginx` | a route, websocket, upload limit or production setup |
| `permissions` | any permission check, role, `can_*` flag |
| `db-integrity` | new field, model or migration |
| `er-diagram` | draw the schema; compare old vs new |
| `unit-test` | writing or running tests |
| `background-jobs` | slow or scheduled work (Celery + Redis) |
| `signals` | a save must trigger something |
| `api-doc` | new or changed endpoint -> Apidog |
| `frontend-handoff` | flow, payloads, walkthrough for the frontend |
| `clean-code-review` | after tests are green |
| `security-check` | before every PR |
| `pre-pr-check` | "is it ready?" - the full review |
| `translation` | last, before the PR |
| `docker-stack` | running the stack, adding a service |
| `mcp-connection` | optional: expose your services to AI assistants through MCP |

**Your words win**: the user's instruction > `business.md` > this file > a skill's default.

## Testing

`python manage.py test` - plain `TestCase` / `APITestCase`, no pytest.
