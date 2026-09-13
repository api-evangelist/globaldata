---
name: globaldata-connect-and-orient
description: >-
  Connect to a GlobalData Intelligence Center MCP server, authenticate, and find out what data the
  current subscription actually exposes before running any query.
api: GlobalData Intelligence Center MCP
generated: '2026-09-13'
method: generated
source: https://mcp.globaldata.com/ (Sections 04, 05, 08, 13)
grounding: >-
  Every tool named below appears in GlobalData's own published gateway-tool reference (Section 06).
  No tool name, parameter or endpoint here was invented. Input schemas are NOT reproduced — the
  live tools/list requires authentication and returned HTTP 401 to this pipeline.
operations:
  - list_domains
  - get_capabilities
  - tool_search
---

# Connect to GlobalData and find out what you can see

GlobalData gates two different things separately, and confusing them wastes calls. **Entitlement**
decides what your subscription contains. **Progressive discovery** decides what your client can
currently see. Orient on both before querying.

## 1. Pick the vertical

The endpoint is one per vertical: `https://mcp.globaldata.com/{site}/mcp`.

If you do not know which slug you need, read
`https://mcp.globaldata.com/.well-known/ai-catalog.json` — it is public, needs no token, and lists
every vertical endpoint with a description and representative queries.

## 2. Authenticate

Every endpoint returns `401 {"error":"Unauthorized: token required"}` without a bearer token.

- Interactive client: OAuth 2.1 Authorization Code + PKCE against GlobalData SSO. Your redirect URI
  must be registered with the Direct Data Solutions team first.
- Server-side agent: `POST https://login.globaldata.com/oauth/token` with `grant_type=password`,
  your username and password, and `scope=openid profile email offline_access`. The token is a
  bearer token with `expires_in: 3600`.

Send it as `Authorization: Bearer <token>` on every request.

## 3. Orient with `list_domains`

Call `list_domains` first. It returns every domain available for this vertical, its unlock status,
and the exact command to reveal each one. GlobalData names this as the connection-verification
step: a clean domain list confirms authentication, transport and progressive discovery all work.

If a domain you expected is missing, that is an entitlement fact, not a client bug.

## 4. Inspect without committing

- `get_capabilities` returns the full domain and tool catalog **without revealing or enabling
  anything**. Use it to plan a multi-step sequence before unlocking anything.
- `tool_search` finds tools by keyword across all domains, including unrevealed ones — useful when
  you know the data type ("analytics", "patent", "project") but not the domain.

Neither call changes session state, so both are safe to run first.

## 5. Reveal only what you need

- `discover_capabilities('<domain>')` reveals that domain's standard tools.
- `reveal_advanced('<domain>')` additionally reveals its analytics tools.

Both emit `notifications/tools/list_changed`.

## Failure modes worth knowing

- **`not_entitled`** — your subscription does not include that domain. Re-revealing will never
  work; this needs an account conversation, not a retry.
- **"tool not found" right after a successful reveal** — the tool is enabled server-side but your
  client's local tool list has not refreshed. Call `search(domain=..., keywords=...)` instead,
  which does not depend on your local list, or re-fetch `tools/list` and confirm the tool appears
  before calling it by name.
- Claude Desktop needs a full app restart to reload tool definitions; claude.ai needs the connector
  toggled off and on; Copilot Studio needs the MCP tool re-synced and the agent republished.
