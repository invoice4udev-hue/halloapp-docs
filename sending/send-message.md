# Send a WhatsApp Message

Sends a WhatsApp message to a phone through the token's desk. The message is stored as a HalloApp conversation so replies thread back to the desk's agents.

The desk is taken from the bearer token — there is no `deskGuid` parameter.

## Endpoint

| | |
| - | - |
| **Method** | `POST` |
| **Path** | `/api/halloapp/whatsapp/send` |
| **Auth** | Bearer token |

## Provider rules

| Desk provider | `message` | `templateId` + `templateParams` | `fileUrl` |
| --- | --- | --- | --- |
| **InforU** | ignored (template defines the text) | **required**; param count must match `parameterCount` | put the URL **inside** `templateParams` |
| **Green API** | required (or `fileUrl`) | rejected | appended to the text as a link |

Discover which mode your desk uses via [List Templates](../discovery/templates.md) (`isInforu`).

## Request schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `phone` | string | Yes | Local (`05...`) or international format. |
| `name` | string | No | Used when a new chat/contact is created. |
| `agentId` | integer | No | Attribute the message to this desk agent; omitted = system message. |
| `message` | string | Conditional | Green API desks: the message text. |
| `templateId` | integer | Conditional | InforU desks: **required** (from [Templates](../discovery/templates.md)). |
| `templateParams` | string[] | Conditional | One value per `[#n#]` placeholder, in order. |
| `fileUrl` | string | No | Document link (see provider rules). |

## Example request — free text (Green API desk)

```http
POST /api/halloapp/whatsapp/send HTTP/1.1
Host: app.halloapp.co.il
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "phone": "0501234567",
  "name": "Israel Israeli",
  "message": "Hello! Your document is ready:",
  "fileUrl": "https://myapp.example/reports/vat-2026-06.pdf"
}
```

## Example request — template (InforU desk)

```http
POST /api/halloapp/whatsapp/send HTTP/1.1
Host: app.halloapp.co.il
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "phone": "0501234567",
  "name": "Israel Israeli",
  "agentId": 12,
  "templateId": 17,
  "templateParams": ["Israel Israeli", "VAT report", "2026-06", "My Business Ltd", "https://myapp.example/reports/vat-2026-06.pdf"]
}
```

## Example response

```json
{
  "ok": true,
  "chatId": 92,
  "messageId": 4587,
  "status": 0
}
```

### Response fields

| Field | Type | Description |
| --- | --- | --- |
| `ok` | boolean | `true` on success; `false` on a business error (with `msg`). |
| `chatId` | integer | HalloApp conversation id (created if it didn't exist). |
| `messageId` | integer | HalloApp message record id. |
| `status` | integer | Provider status; `>= 0` means the provider accepted the message. |
| `msg` | string | Present only when `ok: false` — see errors below. |

## Errors

Business errors return HTTP 200 with `ok: false` and a `msg`:

| `msg` | Cause / fix |
| --- | --- |
| `phone is required` / `invalid phone` | Supply a valid phone. |
| `agent not found in this desk` | `agentId` must be an active member of the token's desk. |
| `this desk requires a templateId (see GET /api/halloapp/whatsapp/templates)` | InforU desk — send a `templateId`. |
| `this desk does not support templates - send free text instead` | Green API desk — omit `templateId`, send `message`. |
| `message or fileUrl is required` | Free-text send needs at least a `message` or a `fileUrl`. |
| `template expects N parameter(s), got M` | `templateParams` count must equal the template's `[#n#]` count. |
| `template not found for this desk` | The `templateId` doesn't belong to this desk. |
| `on this desk files are delivered as a link inside a template parameter - include fileUrl as one of templateParams` | InforU desk — pass `fileUrl` as one of `templateParams`. |

A missing or expired token returns **HTTP 401**.

## Try it

{% openapi-operation spec="halloapp-api" path="/api/halloapp/whatsapp/send" method="post" %}
{% endopenapi-operation %}
