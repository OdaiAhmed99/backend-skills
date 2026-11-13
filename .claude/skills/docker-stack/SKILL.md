---
name: docker-stack
description: Running the backend - docker compose (Postgres, Redis, backend, celery, beat) or a local venv with SQLite and no Redis - and the .env. Use when running the stack, adding a service, or changing ports/env.
---
# Running the stack

```
:8000 backend (daphne: HTTP + websockets)
      celery  (worker)      -> redis
      beat    (schedules)   -> redis
      db      (postgres)
```

- **Docker**: `docker compose up -d --build`, logs `docker compose logs -f backend celery`, tests `docker compose exec backend python manage.py test`.
- **Local, no Docker**: venv + `pip install -r backend/requirements.txt`, `python backend/manage.py migrate`, `runserver`. Empty `POSTGRES_HOST` -> SQLite; empty `REDIS_URL` -> in-memory cache and channels, Celery tasks run inline.

## Rules
- Secrets only in `.env`; every variable also listed (empty or fake) in `.env.example`.
- Celery and beat use the same image and env as the backend.
- A new service: add it to compose, its env vars to `.env.example`, and a line here.
- "It's stuck": `docker compose down` then up again - never leave half a stack running. `down -v` deletes the database: ask first.
