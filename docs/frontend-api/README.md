# Frontend API handshake

What the frontend team builds from. One folder per backend app, each with exactly three files:

- `flow.md` - which call, when, by whom
- `payloads.md` - exact request and response bodies
- `walkthrough.md` - the story of the screen, step by step

Field detail must match Apidog. If they disagree, tell the backend.

## Layout

- `shared/` - login, pickers, errors, pagination envelope, websocket
- _add a line per app and page_
