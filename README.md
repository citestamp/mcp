# CiteStamp MCP server

Ground citations before your agent emits them.

[CiteStamp](https://citestamp.com) is a free citation-health tool for researchers: it
checks references against the public scholarly registries (Crossref, DataCite, OpenAlex)
and flags the ones that **do not resolve to a known work** or that **a publisher has
marked retracted**, the hallucinated-citation failure mode LLM users keep hitting.

This repo is the install guide for the **hosted MCP server** over CiteStamp's CC0
citation graph. There is nothing to build or run locally; the server is remote.

| | |
|---|---|
| Endpoint | `https://mcp.citestamp.com/mcp` |
| Transport | streamable-http |
| Protocol | `2025-06-18` |
| Auth | None. Anonymous at 10 requests/min per IP. An optional free key (email info@citestamp.com) raises that to 60/min; Pro to 600/min |
| Data | CC0 citation graph; human-signed and machine-inferred claims kept strictly apart |

## The tools (all read-only)

- `what_supports` / `what_refutes`: typed edges bearing on a claim
- `what_cites` / `references_of`: the citing and cited works around a reference
- `edges_about`: edges on a topic
- `retraction_listing`: whether the Retraction Watch database lists a DOI as retracted, from the dated
  export CiteStamp joins weekly. Not listed means not in that export; it does not check the publisher record
- `check_reference`: whether a reference resolves to a real work, before an answer cites it. Give a DOI,
  an arXiv id or the reference as written. It reads Crossref, DataCite and doi.org (which answers for every
  DOI agency) and says found, confirm (with the reason), not found in the named sources on a date, or
  couldn't check. A not-found is not proof that a work was never published

Every result carries its tier: **accountable** edges are signed by a human identity
(ORCID); **inferred** edges are machine-derived. Your agent can ask for signed-only.

## Add it to your client

No key is needed. Every snippet below works as written.

### Gemini CLI (extension)
```bash
gemini extensions install https://github.com/citestamp/mcp
```
Or add the server by hand in `~/.gemini/settings.json`:
```json
{ "mcpServers": { "citestamp": { "httpUrl": "https://mcp.citestamp.com/mcp" } } }
```

### Gemini Spark (custom app)
In the Gemini web app: Settings & help → Connected Apps → under "Custom apps for Spark"
click **Add a custom app** → enter `https://mcp.citestamp.com/mcp` → Next. Type `@` in a
prompt and pick CiteStamp to make Gemini use it. Custom apps need access to Gemini Spark
on a personal Google Account with Keep Activity on.

### Claude (claude.ai / Desktop)
Settings → Connectors → **Add custom connector** → URL `https://mcp.citestamp.com/mcp`.
Leave authentication empty.

### Claude Code
```bash
claude mcp add --transport http citestamp https://mcp.citestamp.com/mcp
```

### Cursor (`~/.cursor/mcp.json`)
```json
{ "mcpServers": { "citestamp": { "url": "https://mcp.citestamp.com/mcp" } } }
```

### Cline (MCP settings)
```json
{ "mcpServers": { "citestamp": {
    "type": "streamableHttp",
    "url": "https://mcp.citestamp.com/mcp" } } }
```

### VS Code (`.vscode/mcp.json`)
```json
{ "servers": { "citestamp": {
    "type": "http",
    "url": "https://mcp.citestamp.com/mcp" } } }
```

If a snippet drifts from your client's current config schema, your client's MCP docs
win; the endpoint is the stable part.

## Optional: raise the rate limit

A key is never required. If you hold one, send it as a header on the same endpoint:

```
Authorization: Bearer <your key>
```

For Claude Code that is `--header "Authorization: Bearer <your key>"`; for the JSON
configs, add `"headers": { "Authorization": "Bearer <your key>" }` to the server entry.
A bad key is a loud 401, never a silent downgrade to anonymous, so add the header only
once you have a key.

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
