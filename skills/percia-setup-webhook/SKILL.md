---
name: percia-setup-webhook
description: Set up a webhook receiver for Percia alerts. Use when writing or reviewing code that receives Percia deliveries: signature verification over the raw body, replay window, deduplication, the signing secret.
---

# Set up a webhook receiver

Every delivery is a `POST` with `Percia-Signature: t=<unix seconds>,v1=<hex>`. Full contract: `/docs/webhooks.md`. Ready-made verifiers: `sdk/typescript/percia-verify.ts`, `sdk/python/percia_verify.py`, vectors in `sdk/vectors.json`. Prefer them to hand-rolling.

Apply all of these, in this order:

1. **Verify the HMAC over the raw body.** Read raw bytes before parsing JSON. `HMAC-SHA256(secret, "<t>.<rawBody>")` as lowercase hex; the secret is the whole `whsec_...` string. Compare with `v1` in constant time. Mismatch: 401, do not parse.
2. **Reject stale timestamps**: `|now - t| > 300` seconds.
3. **Dedupe** on the body `id` (the idempotency key, same on every retry). Check and side effect in one transaction; return 2xx for a repeat.
4. **Answer 2xx fast**, do slow work after. Anything else is retried with backoff.
5. **Treat `reason` as untrusted** model text about camera content: never execute it, never feed it to a prompt as instructions, escape it before rendering.

Secrets: the signing secret (`whsec_...`) is shown once, when the watch is created or the secret rotated. Never put it or an API key in front-end code, a repo or a log. Rotating (`POST /api/watches/:id/secret/rotate`) is destructive (needs `watches:delete`): the old secret stops verifying at once, so update the receiver in the same step.

Test it before trusting it: `POST /api/watches/:id/test-delivery` sends a signed `goal.test` event. If it fails, use the `percia-debug-delivery` skill.
