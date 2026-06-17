---
name: agentfinder
description: >-
  Find installable MCP servers, tools, and agents for a task by searching
  GitHub's Agent Finder. Use whenever the user wants to find or install a tool,
  MCP server, integration, or agent for something they're trying to do —
  email, calendars, databases, payments, cloud platforms, CI/CD, messaging,
  monitoring, file storage, and similar third-party services.
argument-hint: <what you want to find>
---

# Find tools with GitHub's Agent Finder

Use this skill when the user asks you to **find** an MCP server, tool, or agent
for a task. It searches **GitHub's Agent Finder** — a registry of installable
MCP servers and extensions — and presents matches for the user to choose from.

Invoke it as `/agentfinder <query>`, where `<query>` is the task you want
to find tools for. Also use it whenever the user otherwise asks you to find a
tool, MCP server, or integration for a task.

When the user's task clearly needs a third-party service (email, calendars,
payments, databases, cloud platforms, CI/CD, monitoring, messaging, file
storage), search the registry. Don't search for purely local work (writing
code, editing files, git, shell commands, math).

## 1. Query the Agent Finder

Send the user's task to the search endpoint. Use whatever HTTP capability you
have (in a terminal, `curl`):

```bash
curl -s https://agentfinder.github.com/api/v1/search \
  -H 'Content-Type: application/json' \
  -d '{"query":{"text":"<the user's task, in plain language>"}}'
```

- The body follows the ARD spec: a `query` object with a `text` field
  (e.g. `{"query":{"text":"send email via gmail"}}`). An optional structured
  `query.filter` (e.g. `{"type":["application/mcp-server+json"]}`) narrows results.
- Add `"pageSize": <n>` to cap the number of results.
- No authentication is required.

## 2. Present the results

The response is `{ "results": [ ... ] }`. Each result has `displayName`,
`mediaType` (the resource type, e.g. `application/mcp-server+json`), `url`,
`identifier`, `source`, and a relevance `score`.

Show a numbered list — for each: **displayName**, the type (`mediaType`), the
`url`, and the `score`. State that the score is **relevance only** — not a trust
or safety rating.

## 3. Never auto-install

Do not add, enable, connect, or install any returned resource yourself.
Installation is always the user's explicit choice.

## 4. Install only on request

Once the user picks a result, show them how to add **that** resource using its
`url`:

- `application/mcp-server+json` — add it as an MCP server (e.g. a `.vscode/mcp.json`
  or `claude_desktop_config.json` entry, or your client's "add MCP server" flow),
  pointed at the resource's `url`.
- `application/ai-skill` — install the skill from its `url`.
- otherwise — connect to it at its `url` over its own protocol.

Then stop and let the user act.
