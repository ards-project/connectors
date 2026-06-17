# ChatGPT Skill — Find agentic resources (ARD)

ChatGPT Skills are reusable, shareable instruction sets (beta; available on
Business, Enterprise, Edu, Teachers, and Healthcare plans). Create a new Skill
and paste the body below as its instructions.

**Querying needs an HTTP capability.** Pair this Skill with either an Agent Finder
**remote MCP connector** (see `mcp/chatgpt/`) or a custom **Action** whose
OpenAPI calls the Agent Finder `POST /search` endpoint. The Skill supplies the
behavior; the connector or Action makes the call.

---

## Skill name

`agentfinder`

## Skill description (used for triggering)

Discover tools, skills, MCP servers, and agents for a task by searching ARD
discovery services (Agent Finder). Use when the user wants to find a tool, skill,
agent, MCP server, API, or capability. Offers a menu of named Agent Finders,
remembers the choice, presents the ranked results, and never installs anything
automatically.

## Skill instructions

When the user asks you to find tools, skills, agents, MCP servers, or other
capabilities for a task, follow these steps in order:

1. **Pick an Agent Finder from a menu, and remember it.** Agent Finders are listed
   — each with a `name` — in the shared `agent-finders.json` config (and, where the
   client can read/write files, in `~/.agentfinder/finders.json`). Two ship by
   default: **GitHub Agent Finder** and **Hugging Face Discover**. The first time,
   show a numbered menu (name + description) and let the user pick; **remember** the
   choice — save it to `~/.agentfinder/finders.json`'s `selected` if you have file
   access, otherwise for the rest of the session — and don't ask again. The user can
   say *switch agent finder* to change. If you reach Agent Finder through a single
   fixed MCP connector or Action, that configured endpoint is the finder — skip the
   menu.

2. **Query the chosen finder.** Send an ARD search request to the selected finder's
   `search` URL: `POST <search URL>` with body
   `{ "query": { "text": "<the user's task>" } }`. Add a `filter`
   (e.g. `{ "type": ["application/mcp-server+json"] }`) to narrow.

3. **Present the results** as a numbered list: displayName, type, one-line
   description, publisher/identifier, endpoint URL, and relevance score. Say the
   score is relevance only — not a trust or safety rating. Offer to follow any
   referrals.

4. **Never auto-install.** Do not add, enable, connect, install, or invoke any
   returned resource yourself.

5. **Install only on request.** After the user picks one, give the steps to
   install or connect that resource themselves, then stop.
