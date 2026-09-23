# Authentication Overview

The HalloApp Send API uses **OAuth-style client credentials**. You exchange your desk credentials for a short-lived bearer token, then send that token on every other call.

### Credentials

HalloApp issues two values out-of-band:

| Value | Meaning |
| --- | --- |
| `client_id` | Your **desk GUID**. |
| `client_secret` | The desk's **API secret**. |

### Token flow

1. `POST /api/halloapp/token` with `client_id` + `client_secret`.
2. Receive an `access_token` (a signed JWT) valid for **240 seconds (~4 minutes)**, reusable until it expires.
3. Send it as `Authorization: Bearer <access_token>` on every other endpoint.

The token is scoped to the issuing desk, so send/discovery endpoints never take a `deskGuid` — the desk is read from the token.

## Endpoint

| | |
| - | - |
| **Method** | `POST` |
| **Path** | `/api/halloapp/token` |
| **Auth** | None (this is the bootstrap that mints the token) |

## Request schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| `client_id` | string | Yes | Your desk GUID. |
| `client_secret` | string | Yes | The desk's API secret. |

## Example request

```http
POST /api/halloapp/token HTTP/1.1
Host: appqa.halloapp.co.il
Content-Type: application/json

{
  "client_id": "11111111-2222-3333-4444-555555555555",
  "client_secret": "your-desk-api-secret"
}
```

## Example response

```json
{
  "ok": true,
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 240
}
```

## Errors

| Condition | Result |
| --- | --- |
| Missing `client_id` / `client_secret` | HTTP 200, `{ "ok": false, "msg": "client_id and client_secret are required" }` |
| Wrong `client_id` / `client_secret` | HTTP 401 Unauthorized |
| Desk not found for the id | HTTP 200, `{ "ok": false, "msg": "desk not found for the supplied client_id" }` |

## Try it

{% openapi-operation spec="halloapp-api" path="/api/halloapp/token" method="post" %}
{% endopenapi-operation %}
