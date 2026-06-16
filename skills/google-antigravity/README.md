# Google Antigravity — Find agentic resources (ARD)

Google Antigravity supports custom skills. To extend Antigravity's capabilities, you can install the `find-agentic-resources` skill either globally or locally for your workspace.

## Installation

1. Create a directory named `.agents/skills/find-agentic-resources/` at the root of your workspace. For installation as a global skill, use `~/.gemini/config/skills/find-agentic-resources/` instead. [refer documentation for more details](https://antigravity.google/docs/skills).

2. Create a sub-directory called `resources` and add `agent-finders.json` (refer the example file in this repo) to it.

3. Create a `SKILL.md` file with the following skill definition content.


Note: Antigravity CLI also supports Agent Skills. Refer [Antigravity CLI documentation](https://antigravity.google/docs/cli-plugins#agent-skills) for specific installation details.

---

## Skill Definition

Create the `SKILL.md` file with the following content:

````yaml
---
name: find-agentic-resources
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

**Requirements.** Querying an endpoint requires HTTP capability. Antigravity can invoke a command like `curl` to query the endpoint.

Follow this contract exactly:

## 1. Ask first — never query silently

Do not call any endpoint yet. Ask the user **which Agent Finder endpoint(s)** to
search. Present the options from the user's `agent-finders.json` list (from the
connectors repository) and let them pick, confirm, or supply a different one.
There is **no built-in default** — the shipped entries are placeholders.

## 2. Query the chosen endpoint(s)

```http
POST <search-endpoint>
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

````