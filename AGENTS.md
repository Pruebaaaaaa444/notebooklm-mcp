# Base44 dev notes — notebooklm-mcp

## What this app is
- A **TypeScript MCP server** (no web UI). It drives a real Chrome via Patchright to
  automate Google NotebookLM (chat, source ingestion, audio overviews).
- Two transports: `stdio` (default) and Streamable-HTTP (`/mcp` endpoint, `/healthz` probe).

## How it runs in Base44
- `docker-compose.base44.yml` runs `tsx watch src/index.ts` (live reload) in HTTP mode
  on port 3000 (`NOTEBOOKLM_TRANSPORT=http`, `NOTEBOOKLM_PORT=3000`, `NOTEBOOKLM_HOST=0.0.0.0`).
- `npm ci` runs at container startup (lockfile-pinned), then tsx watch serves live source.
- Health: `GET http://localhost:3000/healthz` → `{"status":"ok"}`.

## Verify
```bash
docker compose -f docker-compose.base44.yml up -d --build
curl -s http://localhost:3000/healthz
# initialize an MCP session:
curl -s -X POST http://localhost:3000/mcp -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-03-26","capabilities":{},"clientInfo":{"name":"curl","version":"0"}}}'
```

## Quirks / limitations
- NotebookLM tools need **Chrome + a logged-in Google session** (persistent profile in
  the container). First-run auth (`setup_auth`) opens a visible browser window — not
  possible in this headless sandbox; tools will report `authenticated=false` until the
  user provides their own auth/profile.
- Optional env vars: `NOTEBOOK_URL`, `LOGIN_EMAIL`, `LOGIN_PASSWORD` (see README config reference).
- Node ≥ 18 required (repo targets Node 22 in the compose image).
