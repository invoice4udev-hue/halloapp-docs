# List Desk Agents

Optional discovery. Returns the desk's active agents so you can attribute a sent message to a specific person via `agentId`. If you don't need attribution, skip this — messages sent without an `agentId` go out as system messages.

The desk is taken from the bearer token.

## Endpoint

| | |
| - | - |
| **Method** | `GET` |
| **Path** | `/api/halloapp/whatsapp/agents` |
| **Auth** | Bearer token |

## Example request

```http
GET /api/halloapp/whatsapp/agents HTTP/1.1
Host: appqa.halloapp.co.il
Authorization: Bearer <access_token>
```

## Example response

```json
{
  "ok": true,
  "agents": [
    { "id": 12, "name": "Agent Name", "email": "agent@example.com" }
  ]
}
```

### Response fields

| Field | Type | Description |
| --- | --- | --- |
| `agents[].id` | integer | The value to pass as `agentId` on [send](../sending/send-message.md). |
| `agents[].name` | string | Agent display name. |
| `agents[].email` | string | Agent email. |

## Errors

| Condition | Result |
| --- | --- |
| Missing / expired token | HTTP 401 |
| Desk not found for token | HTTP 200, `{ "ok": false, "msg": "desk not found for token" }` |

## Try it

{% openapi-operation spec="halloapp-api" path="/api/halloapp/whatsapp/agents" method="get" %}
{% endopenapi-operation %}
