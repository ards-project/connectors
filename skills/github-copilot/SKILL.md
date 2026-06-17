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

## 1. Use GitHub's Agent Finder (built in)

This skill already knows where to search — **GitHub's Agent Finder**:

```
https://agentfinder.github.com/api/v1/search
```

Query it directly. **Never ask the user for a URL** — the endpoint is built in,
so `/agentfinder <task>` works with zero configuration. No authentication is
required.

Use a different service **only if the user explicitly names one** (e.g. Hugging
Face Discover, or one from their `agent-finders.json`). If they give an ARD
service *base* URL (a version root like `https://host/api/v1`), derive the
endpoints from it: append `/search` to search, `/mcp` for its MCP endpoint.

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

**GitHub Copilot** — copy this `github-copilot/` folder into a directory Copilot
scans: `~/.copilot/skills/` (personal) or `.github/skills/` (project). Copilot
also reads `~/.claude/skills/`, so a copy there is picked up too.

```
cp -r connectors/skills/github-copilot ~/.copilot/skills/
```

Then invoke `/agentfinder <query>`.

> This skill defaults to GitHub's Agent Finder with no configuration. For a
> connector that asks which discovery service to use instead, see the generic
> `agentfinder` skill.
