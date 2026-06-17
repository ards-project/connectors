# github-agentfinder skill

A skill for finding installable MCP servers, tools, and agents via **GitHub's
Agent Finder** (`agentfinder.github.com`). Verified working in the GitHub
Copilot CLI and Claude Code.

## How this differs from the generic `agentfinder` skill

The generic [`agentfinder`](../agentfinder/) skill asks which discovery service
to query (it has no built-in default). This skill is hardcoded to **GitHub's**
Agent Finder at `POST https://agentfinder.github.com/api/v1/search`, so it
searches GitHub's registry directly without asking. Both now send the same ARD
spec object body `{"query":{"text":"…"}}` — GitHub's Agent Finder runs the v0.9
protocol in production as of the `github/agentfinder` PR #30 deploy.

GitHub's Agent Finder also exposes an MCP endpoint at
`POST https://agentfinder.github.com/api/v1/mcp`. If you'd rather give your
client a native `search` tool than drive it from this skill, use the connectors
repo's MCP setup pointed at that URL instead.

## Install

- **GitHub Copilot CLI:** copy this folder to `~/.copilot/skills/github-agentfinder/`.
- **Claude Code:** copy to `~/.claude/skills/github-agentfinder/`.

Then invoke `/agentfinder <query>` — e.g. `/agentfinder query a postgres database`.

(The folder is named `github-agentfinder` to sit alongside the generic
`agentfinder` skill, but the skill's `name` is `agentfinder`, so it invokes as
`/agentfinder`. Install only one of the two.)
