---
name: agentfinder
description: >-
  Discover tools, skills, MCP servers, and agents for a task by searching ARD
  discovery services (Agent Finder). Use whenever the user wants to find a tool,
  skill, agent, MCP server, API, or capability for something they are trying to
  do. Offers a menu of named Agent Finders, remembers the choice, presents the
  ranked results, and never installs anything automatically.
argument-hint: <what you want to find>
---

# Find agentic resources (ARD)

Invoke this skill as `/agentfinder <query>`, where `<query>` is the task the user
wants to find resources for. Also use it whenever the user otherwise asks you to
**find** tools, skills, agents, MCP servers, or other capabilities for a task. It
searches ARD discovery services (Agent Finders) and presents matches for the user
to choose from.

**Requirements.** Querying a finder needs an HTTP capability — in Claude Code,
`Bash` with `curl`; or an Agent Finder **MCP connector** (see `mcp/claude/` in
this repo); or a fetch/web tool. If none is available, tell the user and point
them at the MCP connector setup.

Follow this contract exactly:

## 1. Choose an Agent Finder (a menu the user sees only once)

Agent Finders are listed in a shared config at `~/.agentfinder/finders.json`,
each with a `name`. The user's choice is remembered there, so this is a one-time
menu — not a question on every search.

1. **Seed it if missing.** If `~/.agentfinder/finders.json` does not exist, create
   the directory and write this default:

   ```json
   {
     "selected": null,
     "finders": [
       {
         "id": "github",
         "name": "GitHub Agent Finder",
         "description": "GitHub's public catalog of installable MCP servers, skills, and tools.",
         "search": "https://agentfinder.github.com/api/v1/search",
         "mcp": "https://agentfinder.github.com/api/v1/mcp"
       },
       {
         "id": "huggingface",
         "name": "Hugging Face Discover",
         "description": "Hugging Face's discovery service for agentic resources.",
         "search": "https://huggingface-hf-discover.hf.space/search",
         "mcp": "https://huggingface-hf-discover.hf.space/mcp"
       }
     ]
   }
   ```

2. **Use the saved choice.** Read the file. If `selected` names a finder, use it
   without prompting — say once: *"Searching **the saved finder's name** — say
   *switch agent finder* to change."* Then go to step 2.

3. **Otherwise, show the menu.** Present the finders as a numbered list (name +
   description) and let the user pick by number or name:

   ```
   Which Agent Finder should I search?
     1. GitHub Agent Finder — GitHub's public catalog of MCP servers, skills, and tools
     2. Hugging Face Discover — Hugging Face's discovery service
   (Add your own in ~/.agentfinder/finders.json.)
   ```

   Save the pick by writing its `id` to `selected` in the file, then continue.

When the user says *switch agent finder* (or similar), re-show the menu and update
`selected`. If you have **no file access** (e.g. claude.ai or Desktop over the MCP
connector), there's nothing to choose — just search the endpoint that connector is
configured with.

## 2. Query the chosen Agent Finder

```http
POST <the selected finder's "search" URL>
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
