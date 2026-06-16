# Google Antigravity — Consume ARD via remote MCP

Connect Google Antigravity to an Agentic Resource Discovery (ARD) service as a **remote MCP** connector.

> Replace `https://agent-finder.example.org/mcp` with your ARD MCP endpoint.

## Custom MCP Server Configuration

To register the ARD MCP server, add the configuration to your Antigravity global configuration file.

1. Open the file **`~/.gemini/config/mcp_config.json`**.
   *Alternatively, you can open this in the editor by clicking the `...` dropdown in the side panel → **Manage MCP Servers** → **View raw config**.*

2. Add `agent-finder` to the `mcpServers` object (see the example `mcp_config.json` file in this directory):

```json
{
  "mcpServers": {
    "agent-finder": {
      "serverUrl": "https://agent-finder.example.org/mcp"
    }
  }
}
```

---

## Antigravity CLI

The Antigravity CLI also supports Model Context Protocol (MCP) integrations:

### Configuration Files
- **Workspace-specific Configuration**: Add the MCP server config to **`.agents/mcp_config.json`** at the root of your project. This configuration stays with your repository.
- **Global Configuration**: Add the MCP server config to **`~/.gemini/antigravity-cli/mcp_config.json`**.

Use the same `mcpServers` JSON structure as detailed above (using the `serverUrl` field).