# Discovery Overview

Discovery endpoints let you learn how a desk is configured before you send. Both are `GET` and read the desk from the bearer token.

| Endpoint | Purpose |
| --- | --- |
| [List Approved Templates](templates.md) | Find out whether the desk sends templates (InforU) or free text (Green API), and which `templateId` values are available. |
| [List Desk Agents](agents.md) | Optional — find the `agentId` values you can attribute messages to. |

### Typical order

1. Call [Templates](templates.md) once to determine the provider mode (`isInforu`).
2. If you want messages attributed to a person, call [Agents](agents.md) to pick an `agentId`.
3. [Send](../sending/send-message.md) using what you discovered.
