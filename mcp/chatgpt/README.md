# ChatGPT — Agent Finder via remote MCP

ChatGPT supports **remote MCP servers only** (HTTPS, Streamable HTTP / SSE),
through **Developer mode**. The Agent Finder is the MCP server.

> Requires a plan with Developer mode (currently Plus, Pro, Business, Enterprise,
> Education). Replace `https://agent-finder.example.org/mcp` with your Agent Finder's MCP
> endpoint.

## Add the connector

1. **Settings → Apps → Advanced settings → Developer mode** (enable it).
2. **Create app** next to Advanced settings.
3. Name it `Agent Finder` and paste the **remote MCP URL**
   (`https://agent-finder.example.org/mcp`).
4. Choose authentication — **No authentication** for an open Agent Finder, or
   **OAuth** if it requires sign-in.
5. Save. The Agent Finder's search tool is now available in chat.

## Behavior

Pair it with the **`agentfinder` Skill** (`../../skills/chatgpt/`) so
ChatGPT asks which endpoint to query, presents the ranked list, and never
auto-installs. The behavior is the shared
[interaction contract](../../shared/discovery-instructions.md).

## No MCP? Use an Action instead

If you can't use Developer mode, add a custom **Action** to a GPT whose OpenAPI
schema calls the Agent Finder REST endpoint `POST /search`, and attach the Skill
instructions. Same behavior, REST instead of MCP.
