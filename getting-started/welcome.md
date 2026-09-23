# Welcome to the HalloApp Send API

The HalloApp Send API lets partner applications send WhatsApp messages through a HalloApp desk — documents, reports, notifications and more — straight from your own system. Messages are stored as normal HalloApp conversations, so replies land back in the desk and are handled by its agents in the HalloApp UI.

### Concepts

* **Desk** — the HalloApp workspace connected to your organization. It is the identity of the caller: every API credential and every message is scoped to one desk.
* **Provider** — each desk sends WhatsApp through one provider, and the rules differ:
  * **InforU desk** — messages MUST go out as a pre-approved template (`templateId` is mandatory).
  * **Green API desk** — free text only (templates are rejected).
* **Files** — always delivered as a **download link**, never as a native WhatsApp attachment. The link must be reachable by the end customer.
* **Chat** — every sent message becomes a HalloApp conversation. If none exists for the target phone it is created (along with a phonebook contact), and customer replies thread into the same chat.

### Get access

Credentials are issued out-of-band by HalloApp. You receive:

1. A **`client_id`** — your desk GUID.
2. A **`client_secret`** — the desk's API secret.

Exchange them at the [Token endpoint](../authentication/overview.md) for a short-lived bearer token, then send that token on every other call.

### Base URLs

| Environment | Base URL |
| ----------- | -------- |
| Production | `https://app.halloapp.co.il` |
| QA (staging) | `https://appqa.halloapp.co.il` |

All endpoints in this documentation are relative to these base URLs. The examples use **Production**; a **QA** environment is available if you'd rather test first. WhatsApp messages are delivered live, so test with your own phone number before sending to customers.

### Request format

Every endpoint is called with a JSON body and returns JSON. Authenticated endpoints expect the bearer token in the `Authorization` header:

```http
POST /api/halloapp/whatsapp/send HTTP/1.1
Host: app.halloapp.co.il
Content-Type: application/json
Authorization: Bearer <access_token>

{ "phone": "0501234567", "message": "Hello!" }
```

### Authentication

OAuth-style **client credentials**. Exchange your `client_id` / `client_secret` at `POST /api/halloapp/token` for a bearer token valid for **4 minutes and reusable until it expires**. Pass it as `Authorization: Bearer <access_token>` on every other endpoint. See the [Authentication Overview](../authentication/overview.md).

### Response & error model

Successful calls return `ok: true` with the operation payload. **Business errors return HTTP 200 with `ok: false` and a `msg`** explaining the fix (missing token returns HTTP 401). Always check `ok` before using the result.

| `msg` contains | Cause / fix |
| --- | --- |
| `client_id and client_secret are required` | Missing credentials on `/token`. |
| `desk not found for token` | Token valid but the desk was removed/archived. |
| `phone is required` / `invalid phone` | Missing or empty phone. |
| `this desk requires a templateId` | InforU desk — send with `templateId` + `templateParams`. |
| `this desk does not support templates` | Green API desk — send free text instead. |
| `template expects N parameter(s)` | `templateParams` count must match the template's `[#n#]` placeholders. |
| `agent not found in this desk` | `agentId` is not an active member of the token's desk. |

### First steps

Follow the [Quick Start](quick-start.md), then read [Key Tips & Rules](key-tips.md) before going live.

### Machine-readable resources

* [OpenAPI 3.0 spec (JSON)](https://raw.githubusercontent.com/invoice4udev-hue/halloapp-docs/main/openapi/halloapp-openapi.json) — the full API surface for code generators, Postman, and AI agents.
* A raw ReDoc rendering of the same spec is also served by the app at `/apidocs`.

### Support

For integration help, contact HalloApp support. Include the endpoint called, the request payload, the response payload, and the environment (QA/Production) with every report.

### Next pages

* [Quick Start](quick-start.md)
* [Authentication Overview](../authentication/overview.md)
* [Sending Overview](../sending/overview.md)
