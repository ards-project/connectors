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
agent, MCP server, API, or capability. Asks which endpoint(s) to query, presents
the ranked results, and never installs anything automatically.

## Skill instructions

When the user asks you to find tools, skills, agents, MCP servers, or other
capabilities for a task, follow these steps in order:

1. **Ask first — never query silently.** Ask the user which Agent Finder
   endpoint(s) to search. Present the options from the user's `agent-finders.json`
   list (from the connectors repository) and let them pick, confirm, or supply a
   different one. There is **no built-in default** — shipped entries are
   placeholders.

2. **Query the chosen endpoint(s).** Send an ARD search request:
   `POST <endpoint>` with body
   `{ "query": { "text": "<the user's task>" } }`.
   Add a `filter` (e.g. `{ "type": ["application/mcp-server+json"] }`) to narrow.

3. **Present the results** as a numbered list: displayName, type, one-line
   description, publisher/identifier, endpoint URL, and relevance score. Say the
   score is relevance only — not a trust or safety rating. Offer to follow any
   referrals.

4. **Never auto-install.** Do not add, enable, connect, install, or invoke any
   returned resource yourself.

5. **Install only on request.** After the user picks one, give the steps to
   install or connect that resource themselves, then stop.
