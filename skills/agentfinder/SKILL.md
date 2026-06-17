---
name: agentfinder
description: >-
  Discover installable MCP servers, tools, skills, and agents for a task by
  searching an ARD Agent Finder. Use whenever the user wants to find or install a
  tool, MCP server, skill, agent, or integration for something they are trying to
  do — email, calendars, databases, payments, cloud platforms, CI/CD, messaging,
  monitoring, file storage, and similar services.
argument-hint: <what you want to find>
---

# Find agentic resources (Agent Finder)

Use this skill when the user asks you to **find** an MCP server, tool, skill, or
agent for a task. It searches an ARD Agent Finder (a discovery service) and
presents matches for the user to choose from.

Invoke it as `/agentfinder <query>`, where `<query>` is the task to find tools
for. Also use it whenever the user otherwise asks you to find a tool, MCP server,
or integration for a task. Search the registry when the task needs a third-party
service (email, calendars, payments, databases, cloud, CI/CD, monitoring,
messaging, file storage); skip it for purely local work (writing code, editing
files, git, shell, math).

## 1. Pick the endpoint

Default to **GitHub's Agent Finder**:

```
https://agentfinder.github.com/api/v1/search
```

If the user names a different ARD discovery service (e.g. Hugging Face Discover,
or one from their `agent-finders.json`), use that endpoint instead. No
authentication is required for GitHub's Agent Finder.

## 2. Query it

Send the user's task as an ARD `query` object. Use whatever HTTP capability you
have (in a terminal, `curl`):

```bash
curl -s https://agentfinder.github.com/api/v1/search \
  -H 'Content-Type: application/json' \
  -d '{"query":{"text":"<the user's task, in plain language>"}}'
```

- The body is the ARD spec shape: a `query` object with a `text` field. Add an
  optional `query.filter` (e.g. `{"type":["application/mcp-server+json"]}`) to
  narrow by resource type, and `"pageSize": <n>` to cap results.

## 3. Present the results

The response is `{ "results": [ ... ] }`. Each result has `displayName`,
`mediaType` (the resource type, e.g. `application/mcp-server+json`), `url`,
`identifier`, `source`, and a relevance `score`. Show a numbered list — for each:
**displayName**, the type, the `url`, and the `score`. State that the score is
relevance only — not a trust or safety rating.

## 4. Never auto-install

Do not add, enable, connect, or install any returned resource yourself.
Installation is always the user's explicit choice.

## 5. Install only on request

Once the user picks a result, show them how to add **that** resource using its
`url`:

- `application/mcp-server+json` — add it as an MCP server (a `.vscode/mcp.json`
  or `claude_desktop_config.json` entry, or your client's "add MCP server" flow),
  pointed at the resource's `url`.
- `application/ai-skill` — install the skill from its `url`.
- otherwise — connect to it at its `url` over its own protocol.

Then stop and let the user act.

## Installation

**Claude Code** — add this repo as a plugin marketplace and install:

```
/plugin marketplace add ards-project/connectors
/plugin install agentfinder@ard-connectors
```

Or copy this `agentfinder/` folder into `~/.claude/skills/` (personal) or
`.claude/skills/` (project).

**GitHub Copilot CLI** — copy this `agentfinder/` folder into
`~/.copilot/skills/`.

Then invoke `/agentfinder <query>`.
