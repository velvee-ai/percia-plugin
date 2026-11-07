# Percia for Claude

Watch cameras and video with AI vision from a conversation. Describe what to look for in plain words ("tell me when a person is at the front door"), add a phone as a camera by scanning a QR code, and get an alert, with the reason and a photo of the moment, in Slack, email or a webhook.

This plugin bundles:

- **The Percia connector** (`.mcp.json`): `https://playground.percia.ai/mcp/directory`. Claude can list, create, pause and review watches, show pairing and connect links as QR cards, and look at what each watch saw. Sign in with your Percia account when asked; you choose what Claude may do on the consent screen, and deleting is off unless you turn it on.
- **Task skills** (`skills/`): connect a camera, create a watch, send alerts to Slack and other tools, review events, debug a delivery, build an agent, set up a webhook receiver.

## Use it

1. Add the plugin (Claude chat, Cowork or Claude Code) and connect Percia when asked.
2. Ask, for example: "Watch my front door camera for a person and tell me in Slack." Percia delivers by itself; Claude does not need to keep checking.

You need a free Percia account (research preview): https://playground.percia.ai. Alerts to Slack, Home Assistant or a webhook are connected by you on Percia's own page: Claude can only hand you the link.

## Good to know

- Percia is a research preview and not a safety or security device. Only connect cameras you own or are allowed to monitor.
- What Percia keeps and who processes it: https://privacy.percia.ai/privacy/
- Terms: https://www.percia.ai/terms
- Docs for developers and agents: https://playground.percia.ai/coding-agents and https://playground.percia.ai/llms.txt
- Support: support@velvee.ai

## Maintenance

The skills are copied from the Percia application repository (`scripts/sync-plugin.mjs`); change them there, not here. The connector is the directory edition of the MCP server: it has `read`, `write` and `delete` tools instead of a code-execution tool.

License: MIT.
