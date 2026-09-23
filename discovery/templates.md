# List Approved Templates

Returns the desk's approved WhatsApp templates. Use it first to learn how your desk sends: `isInforu: false` (empty list) means the desk sends **free text** — skip templates entirely; `isInforu: true` means every message must use one of the returned templates.

The desk is taken from the bearer token.

## Endpoint

| | |
| - | - |
| **Method** | `GET` |
| **Path** | `/api/halloapp/whatsapp/templates` |
| **Auth** | Bearer token |

## Example request

```http
GET /api/halloapp/whatsapp/templates HTTP/1.1
Host: app.halloapp.co.il
Authorization: Bearer <access_token>
```

## Example response

```json
{
  "ok": true,
  "isInforu": true,
  "templates": [
    {
      "id": 17,
      "inforuTemplateId": 255201,
      "nameHe": "מסמך i4u",
      "nameEn": "i4u_doc_en_5",
      "messageText": "שלום [#1#], קיבלת [#2#] מספר [#3#] מאת [#4#]. קישור להורדת המסמך: [#5#] . תודה.",
      "language": 1,
      "category": 1,
      "parameterCount": 5
    }
  ]
}
```

### Response fields

| Field | Type | Description |
| --- | --- | --- |
| `isInforu` | boolean | `true` = the desk must send templates; `false` = free text. |
| `templates[].id` | integer | The value to pass as `templateId` on [send](../sending/send-message.md). |
| `templates[].inforuTemplateId` | integer | The provider-side template id. |
| `templates[].nameHe` / `nameEn` | string | Human-readable names. |
| `templates[].messageText` | string | The template body, with `[#n#]` placeholders. |
| `templates[].language` | integer | `1` = Hebrew, `2` = English. |
| `templates[].category` | integer | `1` = Service/Utility, `2` = Marketing. |
| `templates[].parameterCount` | integer | Exact number of `templateParams` values the template expects. |

## Errors

| Condition | Result |
| --- | --- |
| Missing / expired token | HTTP 401 |
| Desk not found for token | HTTP 200, `{ "ok": false, "msg": "desk not found for token" }` |

## Try it

{% openapi-operation spec="halloapp-api" path="/api/halloapp/whatsapp/templates" method="get" %}
{% endopenapi-operation %}
