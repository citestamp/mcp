# Installing the CiteStamp MCP server (for AI assistants)

CiteStamp is a HOSTED (remote) MCP server. There is no package to install, no code to
clone, and nothing to run locally. Setup is two steps:

1. **Get an API key.** Free keys are issued by email: the user emails
   info@citestamp.com and receives a key (60 requests/min). If the user already has a
   CiteStamp Pro token, that works too. There is no anonymous MCP access — a request
   without a valid `Authorization` header returns 401.

2. **Add the remote server to the client's MCP configuration** with:
   - transport / type: `streamableHttp` (a.k.a. streamable-http / http)
   - URL: `https://mcp.citestamp.com/mcp`
   - header: `Authorization: Bearer <the user's key>`

For Cline specifically, the MCP settings entry is:

```json
{
  "mcpServers": {
    "citestamp": {
      "type": "streamableHttp",
      "url": "https://mcp.citestamp.com/mcp",
      "headers": { "Authorization": "Bearer YOUR_KEY" }
    }
  }
}
```

Verification: after configuring, the server should list five read-only tools —
`what_supports`, `what_refutes`, `what_cites`, `references_of`, `edges_about`.
A quick functional test: call `references_of` with a DOI the user knows, e.g.
`10.1371/journal.pmed.0020124`.

Notes for the assistant:
- Do not attempt `npx`, `pip`, `docker`, or any local install — the server is remote only.
- Do not invent an API key or skip the header; a 401 means the key is missing/invalid.
- The tools are read-only; nothing in this server writes anywhere.
