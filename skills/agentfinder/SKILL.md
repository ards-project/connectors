---
name: agentfinder
description: >-
  Discover tools, skills, MCP servers, and agents for a task by searching ARD
  discovery services (Agent Finder). Use whenever the user wants to find a tool,
  skill, agent, MCP server, API, or capability for something they are trying to
  do. Asks which Agent Finder endpoint(s) to query, presents the ranked results,
  and never installs anything automatically.
---

# Find agentic resources (ARD)

Use this skill when the user asks you to **find** tools, skills, agents, MCP
servers, or other capabilities for a task. It searches ARD discovery services
(such as Agent Finder) and presents matches for the user to choose from.

**Requirements.** Querying an endpoint needs an HTTP capability. Use whichever is
available: an Agent Finder **MCP connector** (see `mcp/claude/` in this repo), a
fetch/web tool, or — in Claude Code — `Bash` with `curl`. If none is available,
tell the user and point them at the MCP connector setup.

Follow this contract exactly:

## 1. Ask first — never query silently

Do not call any endpoint yet. Ask the user **which Agent Finder endpoint(s)** to
search. Present the options from the user's `agent-finders.json` list (from the
connectors repository) and let them pick, confirm, or supply a different one.
There is **no built-in default** — the shipped entries are placeholders.

## 2. Query the chosen endpoint(s)

```http
POST <endpoint>
Content-Type: application/json

{ "query": { "text": "<the user's task, in plain language>" } }
```

Narrow results with a filter when useful — e.g. MCP servers only:

```json
{ "query": { "text": "<task>", "filter": { "type": ["application/mcp-server+json"] } } }
```

## 3. Present the results

Numbered list. For each result: **displayName**, **type**, a one-line
**description**, the **publisher / identifier**, the **endpoint URL**, and the
relevance **score**. State that the score is **relevance only** — not a trust or
safety rating. Offer to follow any referrals to other discovery services.

## 4. Never auto-install

Do **not** add, enable, connect, install, or invoke any returned resource
yourself. Installation is always the user's explicit choice.

## 5. Install only on request

Once the user picks a result, give them the steps to install or connect **that**
resource themselves (add it as an MCP connector, install the skill, or call its
API) using the resource's own endpoint and protocol. Then stop and let them act.

## Installation

**Claude Code (recommended)** — add this repo as a plugin marketplace and install:

```
/plugin marketplace add ards-project/connectors
/plugin install agentfinder@ard-connectors
```

**Manual** — copy this `agentfinder/` folder into your Claude Code
skills directory: `~/.claude/skills/` (personal) or `.claude/skills/` (project).

> Custom skills are currently Claude Code–only. The claude.ai web app and Claude
> Desktop do not yet support uploading your own skills.
