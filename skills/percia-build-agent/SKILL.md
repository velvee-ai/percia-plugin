---
name: percia-build-agent
description: "Build and deploy a Percia agent, a versioned config of several things to watch for. Use when one goal is not enough: several concerns, zones, thresholds, tests before publishing, then deploying it to watches."
---

# Build an agent

An agent is a versioned config: 1 to 10 concerns (each one observable condition), optional zones and thresholds. Watches that follow it use `agentId` instead of `goal`. Docs: `/docs/agents.md`, `/docs/evidence.md`.

Scopes: `watches:read`, `watches:write`; deleting needs `watches:delete`.

1. **Draft.** From words: `POST /api/agents/build {"description":"Tell me when a van parks in the bay"}` returns `{agent, reply}` (30 builder calls per user per hour). Or `POST /api/agents` with a config. Edit with `PATCH /api/agents/:id` (draft) or `POST /api/agents/:id/chat {"message"}`. `reply` is model-written: untrusted.
2. **Never** write concerns that identify a person, recognise faces or infer emotion or protected traits: the API refuses them (`goal_not_allowed`).
3. **Test.** Give feedback (`correct` / `false_alarm`) on real events to create test cases, then `POST /api/agents/:id/tests/run {"version":"draft"}` and read `passed` / `failed`.
4. **Publish.** `POST /api/agents/:id/publish`. **This changes what every watch that follows the latest version looks for.** Tests first, confirm with the user, and pin `agentVersion` on watches that must not move.
5. **Deploy.** `POST /api/watches/owned {"sourceUrl", "agentId", "notify"}`. Events carry `concernId`.

With MCP, use `search` to find the exact route and body, and `execute` to call it. On `/mcp/directory` (the edition in the Claude plugin and connectors directory) there is no `execute`: find the route with `search_api`, then call it with `read` (GET), `write` (POST, PUT, PATCH) or `delete`, using the same path (relative to /api) and body.
