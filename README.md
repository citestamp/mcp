# CiteStamp MCP server

Ground citations before your agent emits them.

[CiteStamp](https://citestamp.com) is a free citation-health tool for researchers: it
checks references against the public scholarly registries (Crossref, DataCite, OpenAlex)
and flags the ones that **do not resolve to a known work** or that **a publisher has
marked retracted** — the hallucinated-citation failure mode LLM users keep hitting.

This repo is the install guide for the **hosted MCP server** over CiteStamp's CC0
citation graph. There is nothing to build or run locally — the server is remote.

| | |
|---|---|
| Endpoint | `https://mcp.citestamp.com/mcp` |
| Transport | streamable-http |
| Protocol | `2025-06-18` |
| Auth | None to start — anonymous at 10 req/min per IP. An optional free key (email info@citestamp.com) raises to 60/min; Pro to 600/min |
| Data | CC0 citation graph; human-signed and machine-inferred claims kept strictly apart |

## The five tools (all read-only)

- `what_supports` / `what_refutes` — typed edges bearing on a claim
- `what_cites` / `references_of` — the citing and cited works around a reference
- `edges_about` — edges on a topic

Every result carries its tier: **accountable** edges are signed by a human identity
(ORCID); **inferred** edges are machine-derived. Your agent can ask for signed-only.

## Add it to your client

Replace `YOUR_KEY` with your API key in each snippet.

### Claude (claude.ai / Desktop)
Settings → Connectors → **Add custom connector** → URL `https://mcp.citestamp.com/mcp`
→ add header `Authorization: Bearer YOUR_KEY`.

### Claude Code
```bash
claude mcp add --transport http citestamp https://mcp.citestamp.com/mcp \
  --header "Authorization: Bearer YOUR_KEY"
```

### Cursor (`~/.cursor/mcp.json`)
```json
{ "mcpServers": { "citestamp": {
    "url": "https://mcp.citestamp.com/mcp",
    "headers": { "Authorization": "Bearer YOUR_KEY" } } } }
```

### Cline (MCP settings)
```json
{ "mcpServers": { "citestamp": {
    "type": "streamableHttp",
    "url": "https://mcp.citestamp.com/mcp",
    "headers": { "Authorization": "Bearer YOUR_KEY" } } } }
```

### VS Code (`.vscode/mcp.json`)
```json
{ "servers": { "citestamp": {
    "type": "http",
    "url": "https://mcp.citestamp.com/mcp",
    "headers": { "Authorization": "Bearer YOUR_KEY" } } } }
```

### Gemini CLI (`~/.gemini/settings.json`)
```json
{ "mcpServers": { "citestamp": {
    "httpUrl": "https://mcp.citestamp.com/mcp",
    "headers": { "Authorization": "Bearer YOUR_KEY" } } } }
```

If a snippet drifts from your client's current config schema, your client's MCP docs
win — the endpoint and header are the stable parts.

## What this is not

CiteStamp reports what public registries say at a dated point in time. It does not
judge the truth or quality of any work, and its only verdict vocabulary is
"does not resolve to a known work" and "marked retracted by the publisher".
Statements about works, never about people.

## Links

- Integration guide + REST API: https://citestamp.com/for-ai
- Check a bibliography in the browser (free, nothing uploaded): https://citestamp.com/check
- Reference-health badge for any DOI: https://citestamp.com/citestamped/<doi>
- Privacy: https://citestamp.com/privacy · Contact: info@citestamp.com

CiteStamp is built by 3Rivers Enterprises LLC, Fort Wayne, Indiana.
