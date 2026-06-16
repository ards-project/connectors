# Claude — Agent Finder via remote MCP

Connect Claude to an Agent Finder discovery service as a **remote MCP** connector.
The Agent Finder *is* the MCP server; nothing in this repo runs a server.

> Replace `https://agent-finder.example.org/mcp` with your Agent Finder's MCP endpoint.
> The `/search` path is the REST interface; the MCP interface is usually a
> separate path (e.g. `/mcp`). Use whatever URL your Agent Finder publishes.

## claude.ai / Claude Desktop / mobile (native remote connectors)

1. **Settings → Connectors → Add custom connector.**
2. Name it `Agent Finder` and paste the **remote MCP URL**.
3. If the server requires auth, complete the OAuth sign-in when prompted.
4. Pair it with the **`agentfinder` Skill**
   (`../../skills/agentfinder/`)
   so Claude asks which endpoint to query, presents results, and never
   auto-installs.

## Claude Desktop via local proxy (fallback)

Only needed for older Claude Desktop builds without native remote connectors.
Add this to `claude_desktop_config.json` (see `claude_desktop_config.json` here);
`mcp-remote` bridges the remote server to stdio:

```json
{
  "mcpServers": {
    "agent-finder": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://agent-finder.example.org/mcp"]
    }
  }
}
```

## Behavior

The connector exposes a search tool; the discipline (ask which endpoint, present
the list, never auto-install) comes from the paired Skill or the shared
[interaction contract](../../shared/discovery-instructions.md).
