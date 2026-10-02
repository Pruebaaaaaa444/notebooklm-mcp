# Base44 Dev Environment — notebooklm-mcp

## What this project is
NotebookLM MCP Server (TypeScript). An MCP server that drives a real Chrome via Patchright to interact with Google NotebookLM (chat, source ingestion, audio overviews). Backend-only — no web frontend. Two transports: stdio (default) and Streamable-HTTP.

## How it runs here
- `docker-compose.base44.yml` runs `node:22` with the source bind-mounted at `/app`.
- The server starts in **Streamable-HTTP mode** on port 3000 (`NOTEBOOKLM_TRANSPORT=http`, `NOTEBOOKLM_PORT=3000`, `NOTEBOOKLM_HOST=0.0.0.0`).
- `tsx watch` provides live reload on source edits.
- Health endpoint: `GET /healthz` → `{"status":"ok","protocol":"mcp-streamable-http"}`.
- MCP endpoint: `POST /mcp` (JSON-RPC, requires session init).
- Root `/` returns 404 JSON — this is expected for an API server.

## No secrets required
All config has sensible defaults. `LOGIN_EMAIL`/`LOGIN_PASSWORD` are optional (auto-login is off by default). The server boots without any external credentials. Chrome/Patchright is only launched when a tool is actually invoked, not at startup.

## Useful commands
- Rebuild/restart: `docker compose -f docker-compose.base44.yml up -d --build`
- Logs: `docker compose -f docker-compose.base44.yml logs -f mcp`
- Health check: `curl http://localhost:3000/healthz`
