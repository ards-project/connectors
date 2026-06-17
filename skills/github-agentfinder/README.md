# github-agentfinder skill

A skill for finding installable MCP servers, tools, and agents via **GitHub's
Agent Finder** (`agentfinder.github.com`). Verified working in the GitHub
Copilot CLI and Claude Code.

## Why this is separate from the generic `agentfinder` skill

The generic [`agentfinder`](../agentfinder/) skill speaks the ARD spec: it asks
which discovery service to query and sends the object-shaped body
`{"query":{"text":"…"}}`.

This skill is tied to **GitHub's currently-deployed** Agent Finder, which:

- serves search at `POST https://agentfinder.github.com/api/v1/search`,
- accepts the **legacy string** body `{"query":"…"}` (the object shape returns
  `400` on the deployed build),
- does not yet expose `/api/v1/mcp`.

## To do once the v0.9 protocol ships to production

When the v0.9 upgrade is deployed (`github/agentfinder` PR #30: object-shape
`/api/v1/search` + `/api/v1/mcp`), update this skill to send the spec object
shape — at which point it converges with the generic `agentfinder` skill and
this GitHub-specific variant can be retired or pointed at the MCP endpoint.

## Install

- **GitHub Copilot CLI:** copy this folder to `~/.copilot/skills/github-agentfinder/`.
- **Claude Code:** copy to `~/.claude/skills/github-agentfinder/`.

Then invoke `/agentfinder <query>` — e.g. `/agentfinder query a postgres database`.

(The folder is named `github-agentfinder` to sit alongside the generic
`agentfinder` skill, but the skill's `name` is `agentfinder`, so it invokes as
`/agentfinder`. Install only one of the two.)
