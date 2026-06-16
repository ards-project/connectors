# ARD connectors

Client-side connectors that let a chatbot **discover agentic resources** — tools,
skills, MCP servers, agents, and APIs — by searching [ARD](https://agenticresourcediscovery.org)
discovery services (Agent Finders) such as GitHub's Agent Finder or Hugging Face
Discover.

These are **client-side only** — there is no server here. Each connector just
points your chatbot at an Agent Finder endpoint you choose, over the HTTP
`POST /search` or remote MCP interface.

## What every connector does

Nothing is installed automatically. When you ask it to find tools for a task, it:

1. **Asks** which Agent Finder endpoint(s) to query.
2. **Queries** them (`POST /search`).
3. **Presents** the ranked results (the relevance score is *not* a trust or safety rating).
4. **Installs only on request** — once you pick a result, it shows you how to add that resource yourself.

Canonical behavior: [`shared/discovery-instructions.md`](shared/discovery-instructions.md).

## Install (Claude Code)

This repo is a Claude Code plugin marketplace:

```
/plugin marketplace add ards-project/connectors
/plugin install find-agentic-resources@ard-connectors
```

For ChatGPT, GitHub Copilot, Microsoft Copilot, and Gemini — both Skill and MCP
setup — see **[Connect a chatbot](https://agenticresourcediscovery.org/connect/)**.

## Layout

- [`skills/`](skills/) — Skill / instruction bundles per client
- [`mcp/`](mcp/) — remote MCP connector configs per client
- [`agent-finders.json`](agent-finders.json) — your list of Agent Finder endpoints
