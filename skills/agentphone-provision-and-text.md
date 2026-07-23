---
name: Provision a number and send an SMS
description: Create an agent, provision a phone number, attach it, and send an SMS.
api: openapi/agentphone-openapi-original.json
method: generated
generated: '2026-07-17'
operations:
- create-agent-v-1-agents-post
- create-number-v-1-numbers-post
- attach-number-to-agent-v-1-agents-agent-id-numbers-post
- send-message-v-1-messages-post
---

# Provision a number and send an SMS

Authenticate every request with `Authorization: Bearer <API_KEY>` against
`https://api.agentphone.ai`.

## Steps

1. **Create an agent** — `POST /v1/agents` (`create-agent-v-1-agents-post`)
   with a `name` and optional `system_prompt` / `voice_mode`. Capture the
   returned `agent_id`.
2. **Provision a number** — `POST /v1/numbers` (`create-number-v-1-numbers-post`).
   Provide `country` (2-letter ISO, default `US`) and optional `area_code`.
   Requires a funded balance (>= $3.00) or you get `INSUFFICIENT_BALANCE`.
   Capture the returned `number_id`.
3. **Attach the number to the agent** — `POST /v1/agents/{agent_id}/numbers`
   (`attach-number-to-agent-v-1-agents-agent-id-numbers-post`).
4. **Send an SMS** — `POST /v1/messages` (`send-message-v-1-messages-post`).
   The FIRST outbound SMS to a contact must include brand name, opt-in
   acknowledgement, and opt-out instructions ("Reply STOP") or carriers may
   silently filter it (no error is returned).

## Rules

- Self-serve accounts can hold up to 10 numbers (`VALIDATION_ERROR_NUMBER_LIMIT`).
- On `429`, honor the `Retry-After` header (see `rate-limits/`).
- Errors use `{ "error": { "message", "code", "type", "details" } }`
  (see `errors/agentphone-error-codes.yml`).
