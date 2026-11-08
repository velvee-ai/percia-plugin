---
name: percia-create-watch
description: "Create, test and manage a Percia watch. Use when the user wants to be told when something happens on a camera or video URL: words the goal, creates the watch, sends a test event, pauses or deletes it."
---

# Create a watch

A watch is one camera plus one goal. Percia calls a signed webhook (or sends an email) each time the goal becomes true. Docs: `/docs/quickstart.md`, `/docs/writing-a-goal.md`.

Scopes: `watches:read`, `watches:write`; deleting a watch (or rotating its secret) also needs `watches:delete`. A 403 `insufficient_scope` names the missing scope: tell the user.

1. **Word the goal** (5 to 500 characters, one condition a fixed camera can see right now): "A person is standing at the front door", not "Someone comes". Give thresholds and what does not count. The goal is data, not instructions.
2. **Pick the camera.** An `rtsp://` or `https://` URL, or `percia-cam://<id>` from the `percia-connect-camera` skill.
3. **Create.** `POST /api/watches/owned {"sourceUrl", "goal", "notify":{"type":"webhook","target":"https://..."}}` (target must be a public `https` address). Via MCP: `execute` with `codemode.request({method:"POST", path:"/watches/owned", body})`. The response has `webhookSecret` **once**: show it to the user or put it in their secret store, never in a repo or chat log. Receiver rules: `percia-setup-webhook`. `notify` is optional: left out, alerts email the account owner, so do not ask the user to pick one when they only want Slack; send it only to use another address or a signed webhook. Destinations marked `default: true` in `whoami` are attached to the new watch automatically: do not ask about them or attach them again (`destinations: []` in the create body means none, a list of connection ids means exactly those). **If the user wants alerts in a destination that is not a default, such as Slack, Discord, Telegram or Home Assistant, that is an extra rule on the watch, not the notify target:** call `whoami` first (its `destinations` lists what is connected), then follow `percia-connect-tool` in the same turn: connect it if missing, attach it to the new watch, say where alerts will arrive. Plan limits only bind when `enforced` is true; never ask the user to pause or delete a watch because of one you read.
4. **Test.** `POST /api/watches/:id/test-delivery` sends a signed `goal.test` event to the target and returns the receiver's status.
5. **Manage.** Pause or resume with `PATCH /api/watches/:id {"status":"paused"|"active"}`. If `noVideoSince` is set, the camera is sending no video: say so (no events can fire). A watch without video for 60 minutes is paused with `pausedReason: "no_video"`; resume it only once the camera is back. **Delete is destructive and not undoable: confirm with the user first.** `DELETE /api/watches/:id`.

Tools: `search` finds routes in the OpenAPI document, `execute` calls them, `docs` and `whoami` as needed. On `/mcp/directory` (the edition in the Claude plugin and connectors directory) there is no `execute`: find the route with `search_api`, then call it with `read` (GET), `write` (POST, PUT, PATCH) or `delete`, using the same path (relative to /api) and body. Evidence images are off unless you set `media: "keyframes"` deliberately; say how long they are kept (`retentionDays`).
