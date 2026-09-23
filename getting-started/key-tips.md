# Key Tips & Rules

Practical notes that save integration time. Read this before going live.

### Tokens

* A token is valid for **~4 minutes** (`expires_in` seconds) and is **reusable** until it expires — cache it, don't mint one per message.
* The token is scoped to a single desk (its `iss` is the desk GUID). All send/discovery calls read the desk from the token, so they **never take a `deskGuid` parameter**.
* A missing or expired token returns **HTTP 401**. Refresh by calling `/token` again.

### Provider rules

* Each desk sends through exactly one provider. Discover which via [List Templates](../discovery/templates.md) (`isInforu`).
* **InforU desk** — `templateId` is **mandatory**; free text is ignored (the template defines the text).
* **Green API desk** — free text only; sending a `templateId` is **rejected**.
* Template parameter count **must** match the template's `[#n#]` placeholders. InforU tolerates a mismatch, but Meta silently drops the message — it never arrives, with no error.

### Files

* Files are **always a download link**, never a native WhatsApp attachment.
* On a **Green API** desk, `fileUrl` is appended to the message text as a link.
* On an **InforU** desk, the URL must be passed as **one of the `templateParams`** values (the template defines where it lands).
* The URL must be publicly reachable by the end customer.

### Phone numbers

* Accepts local (`05...`) or international format; the value is normalized server-side.
* An empty or unparseable phone returns `invalid phone`.

### Agents

* `agentId` is **optional**. When provided, the message is attributed to that agent and the chat is assigned to them; when omitted, it is sent as a **system message**.
* The agent must be an active member of the token's desk — discover valid ids via [List Desk Agents](../discovery/agents.md).

### Errors

* Business errors come back as **HTTP 200 with `ok: false` + `msg`** — always check `ok` first.
* On Green API, a successful send always returns a provider message id internally; the API treats a missing one as a failure, so a `ok: true` really means delivered to the provider.

### Environments

* The examples use **Production** (`https://app.halloapp.co.il`). A separate **QA** environment (`https://appqa.halloapp.co.il`) is available if you want to test first — credentials differ per environment. WhatsApp messages are delivered live, so test with your own phone number before sending to customers.
