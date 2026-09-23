# Sending Overview

Sending is a single endpoint — [Send a WhatsApp Message](send-message.md) — but what it accepts depends on the desk's provider. Discover the provider first via [List Templates](../discovery/templates.md) (`isInforu`).

### Provider rules

| Desk provider | `message` | `templateId` + `templateParams` | `fileUrl` |
| --- | --- | --- | --- |
| **InforU** | ignored (the template defines the text) | **required**; param count must match `parameterCount` | put the URL **inside** `templateParams` |
| **Green API** | required (or `fileUrl`) | rejected | appended to the text as a link |

### What happens on send

* The target phone is normalized and matched to an existing chat, or a new **chat + phonebook contact** is created.
* The message is stored so it threads in the HalloApp UI, and any customer reply lands on the same conversation.
* If an `agentId` is supplied, the chat is assigned to that agent; otherwise the message is a **system message**.

Continue to [Send a WhatsApp Message](send-message.md) for the full request schema and examples.
