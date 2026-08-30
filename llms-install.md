# Installing the CiteStamp MCP server (for AI assistants)

CiteStamp is a HOSTED (remote) MCP server. There is no package to install, no code to
clone, and nothing to run locally. Setup is two steps:

1. **No key required.** The server answers anonymously at 10 requests/min per IP.
   Configure it with no credentials and it works. A key is OPTIONAL and only raises the
   rate: a free key by email to info@citestamp.com gives 60/min, a Pro token 600/min.

2. **Add the remote server to the client's MCP configuration** with:
   - transport / type: `streamableHttp` (a.k.a. streamable-http / http)
   - URL: `https://mcp.citestamp.com/mcp`
   - no headers. Only if the user holds a key, add `Authorization: Bearer <key>`.

For Cline specifically, the MCP settings entry is:

```json
{
  "mcpServers": {
    "citestamp": {
      "type": "streamableHttp",
      "url": "https://mcp.citestamp.com/mcp"
    }
  }
}
```

Verification: after configuring, the server should list five read-only tools:
`what_supports`, `what_refutes`, `what_cites`, `references_of`, `edges_about`.
A quick functional test: call `references_of` with a DOI the user knows, e.g.
`10.1371/journal.pmed.0020124`.

Notes for the assistant:
- Do not attempt `npx`, `pip`, `docker`, or any local install. The server is remote only.
- Do not invent a key or send a placeholder token. A bad token is a loud 401, never a
  silent downgrade to anonymous.
- The tools are read-only; nothing in this server writes anywhere.
