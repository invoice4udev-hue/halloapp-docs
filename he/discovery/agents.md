# ‫רשימת נציגי ה-Desk‬

‫גילוי אופציונלי. מחזיר את הנציגים הפעילים של ה-Desk כדי שתוכלו לשייך הודעה שנשלחה לאדם מסוים באמצעות `agentId`. אם אינכם צריכים שיוך, דלגו על זה — הודעות שנשלחות ללא `agentId` יוצאות כהודעות מערכת.‬

‫ה-Desk נקרא מטוקן ה-Bearer.‬

## ‫נקודת קצה‬

| | |
| - | - |
| ‫**מתודה**‬ | `GET` |
| ‫**נתיב**‬ | `/api/halloapp/whatsapp/agents` |
| ‫**אימות**‬ | ‫טוקן Bearer‬ |

## ‫דוגמת בקשה‬

```http
GET /api/halloapp/whatsapp/agents HTTP/1.1
Host: appqa.halloapp.co.il
Authorization: Bearer <access_token>
```

## ‫דוגמת תשובה‬

```json
{
  "ok": true,
  "agents": [
    { "id": 12, "name": "Agent Name", "email": "agent@example.com" }
  ]
}
```

### ‫שדות התשובה‬

| ‫שדה‬ | ‫טיפוס‬ | ‫תיאור‬ |
| --- | --- | --- |
| `agents[].id` | integer | ‫הערך שיש להעביר כ-`agentId` ב[שליחה](../sending/send-message.md).‬ |
| `agents[].name` | string | ‫שם התצוגה של הנציג.‬ |
| `agents[].email` | string | ‫אימייל הנציג.‬ |

## ‫שגיאות‬

| ‫מצב‬ | ‫תוצאה‬ |
| --- | --- |
| ‫טוקן חסר / פקוע‬ | HTTP 401 |
| ‫Desk לא נמצא לטוקן‬ | HTTP 200, `{ "ok": false, "msg": "desk not found for token" }` |

## ‫נסו את זה‬

{% openapi-operation spec="halloapp-api" path="/api/halloapp/whatsapp/agents" method="get" %}
{% endopenapi-operation %}
