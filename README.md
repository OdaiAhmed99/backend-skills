# backend-skills

A Claude Code skill set for **backend developers**: Django REST, Postgres, Redis, Celery, Channels.

It teaches Claude one way of working: design first, one shared API layer, permissions done right, a clear handshake with the frontend team, and a review that checks the contract and not only the code.

> Not a developer, or want the full stack (with a React frontend) generated for you? Use [fullstack-skills](https://github.com/OdaiAhmed99/fullstack-skills) instead.

## Why

Claude can write code fast. Without rules it also writes it differently every time. These skills are the rules: where files go, what an endpoint returns, how it's gated, documented and reviewed. Claude does the typing; you stay the lead who can read, explain and defend every line.

## The cycle

```
business -> screen->API map -> build -> db + ER check -> tests -> Apidog
         -> frontend handshake (flow / payloads / walkthrough) -> contract review -> translation -> PR
```

## What's inside

| Area | Skills |
|---|---|
| Plan | `cycle-flow`, `screen-api-map`, `app-memory` |
| Build | `build-app`, `api-shared-layer`, `shared-lookups`, `permissions`, `background-jobs`, `signals` |
| Data | `db-integrity`, `er-diagram` |
| Hand off | `api-doc` (Apidog), `frontend-handoff` |
| Review | `unit-test`, `clean-code-review`, `security-check`, `pre-pr-check`, `translation` |
| Run / extend | `docker-stack`, `nginx`, `mcp-connection` |

## Use it

**New project:** copy this repo (or "Use this template"), open a terminal in it, run `claude`. Claude reads `CLAUDE.md`, sees `business.md` is unanswered, and asks you about your domain first.

**Existing project:** copy `.claude/skills/` into your project and the "Principles" and "Skills" sections of `CLAUDE.md` into yours. Edit paths in a `SKILL.md` if your layout differs.

**Every project:** copy each skill folder into `~/.claude/skills/` (Windows: `%USERPROFILE%\.claude\skills\`).

## Run the template

```bash
cp .env.example .env
docker compose up -d --build
```

## License

MIT
