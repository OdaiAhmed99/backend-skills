---
name: nginx
description: How nginx sits in front of the backend - one file nginx/default.conf, the routes, headers and limits it must have. Use when adding a route, websocket, upload limit, a new service behind the proxy, or preparing production.
---
# nginx

One folder, one file: `nginx/default.conf`. nginx is the only service that publishes a port; Django stays behind it.

## Routes
| Path | Goes to |
|---|---|
| `/api/`, `/admin/` | `backend:8000` with `Host`, `X-Real-IP`, `X-Forwarded-For`, `X-Forwarded-Proto` |
| `/ws/` | `backend:8000`, HTTP 1.1 + `Upgrade` / `Connection`, long `proxy_read_timeout` |
| `/static/`, `/media/` | served by nginx from the shared volumes, never by Django |
| anything else | `404` |

## Always
- Security headers: `X-Content-Type-Options`, `X-Frame-Options DENY`, `Referrer-Policy`.
- `client_max_body_size` = the biggest upload the API accepts - keep both in sync.
- Django trusts `X-Forwarded-Proto` (`SECURE_PROXY_SSL_HEADER`), so nginx must always set it.

## Production
- HTTPS (443) with HSTS; port 80 only redirects.
- Rate-limit login and other public endpoints (`limit_req_zone`).
- Turn off `server_tokens`.

## Adding something
A new service or path: add an `upstream` + `location` here, the service in `docker-compose.yml`, and one row in the table above. Test with `docker compose exec nginx nginx -t` before reloading.
