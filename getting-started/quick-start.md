# Quick Start

Send your first WhatsApp message in four steps. The examples use the **production** base URL (`https://app.halloapp.co.il`). Messages are delivered live — test with your own phone number first. A **QA** environment is also available (see [Base URLs](welcome.md#base-urls)) if you prefer to test there.

### 1. Get a bearer token

Exchange your desk credentials for a short-lived token (valid ~4 minutes, reusable until expiry).

```http
POST /api/halloapp/token HTTP/1.1
Host: app.halloapp.co.il
Content-Type: application/json

{
  "client_id": "11111111-2222-3333-4444-555555555555",
  "client_secret": "your-desk-api-secret"
}
```

Response:

```json
{ "ok": true, "access_token": "eyJhbGciOiJIUzI1NiIs...", "token_type": "Bearer", "expires_in": 240 }
```

Send `access_token` as `Authorization: Bearer <access_token>` on every call below. See [Authentication Overview](../authentication/overview.md).

### 2. Find out how your desk sends

Ask the desk which provider mode it uses. `isInforu: true` means you must send approved templates; `isInforu: false` means free text.

```http
GET /api/halloapp/whatsapp/templates HTTP/1.1
Host: app.halloapp.co.il
Authorization: Bearer <access_token>
```

See [List Approved Templates](../discovery/templates.md).

### 3. Send a message

**Green API desk (free text):**

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

**InforU desk (approved template):**

```http
POST /api/halloapp/whatsapp/send HTTP/1.1
Host: app.halloapp.co.il
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "phone": "0501234567",
  "name": "Israel Israeli",
  "templateId": 17,
  "templateParams": ["Israel Israeli", "VAT report", "2026-06", "My Business Ltd", "https://myapp.example/reports/vat-2026-06.pdf"]
}
```

See [Send a WhatsApp Message](../sending/send-message.md).

### 4. Check the response

```json
{ "ok": true, "chatId": 92, "messageId": 4587, "status": 0 }
```

`ok: true` with `status >= 0` means the provider accepted the message. On a business error you get `ok: false` and a `msg` — see the error catalog in [Welcome](welcome.md#response--error-model).

### Next

* Read [Key Tips & Rules](key-tips.md) before going live.
* Attribute messages to a specific agent? See [List Desk Agents](../discovery/agents.md).
