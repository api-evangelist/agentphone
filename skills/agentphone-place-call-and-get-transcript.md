---
name: Place an outbound call and read the transcript
description: Register a webhook, place an outbound voice call, then retrieve the transcript.
api: openapi/agentphone-openapi-original.json
method: generated
generated: '2026-07-17'
operations:
- create-or-update-webhook-v-1-webhooks-post
- create-outbound-call-v-1-calls-post
- get-call-v-1-calls-call-id-get
- get-call-transcript-v-1-calls-call-id-transcript-get
---

# Place an outbound call and read the transcript

Authenticate with `Authorization: Bearer <API_KEY>` against
`https://api.agentphone.ai`. The agent must already have a phone number attached.

## Steps

1. **Register a webhook** (webhook voice mode) —
   `POST /v1/webhooks` (`create-or-update-webhook-v-1-webhooks-post`) with your
   HTTPS `url`. Save the returned `secret` (prefix `whsec_`) to verify the
   `X-Webhook-Signature` (HMAC-SHA256 over `{timestamp}.{raw_body}`).
   Skip this step if the agent uses hosted voice mode (built-in LLM).
2. **Place the call** — `POST /v1/calls`
   (`create-outbound-call-v-1-calls-post`) with `agent_id` and the `to` number
   in E.164. Capture the returned `call_id`.
3. **Poll call status** — `GET /v1/calls/{call_id}`
   (`get-call-v-1-calls-call-id-get`) until the call ends (you may also
   receive an `agent.call_ended` webhook event).
4. **Read the transcript** — `GET /v1/calls/{call_id}/transcript`
   (`get-call-transcript-v-1-calls-call-id-transcript-get`).

## Rules

- Verify webhook signatures and reject deliveries whose timestamp is older
  than 5 minutes; de-duplicate on `X-Webhook-ID` (see `conventions/`).
- Retry `429/500/502/503/504` with exponential backoff.
- Events: `agent.message`, `agent.call_ended`, `agent.reaction`
  (see `asyncapi/agentphone-webhooks-asyncapi.yml`).
