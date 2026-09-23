# ‫רשימת תבניות מאושרות‬

‫מחזיר את תבניות ה-WhatsApp המאושרות של ה-Desk. השתמשו בו תחילה כדי ללמוד כיצד ה-Desk שלכם שולח: `isInforu: false` (רשימה ריקה) אומר שה-Desk שולח **טקסט חופשי** — דלגו על תבניות לגמרי; `isInforu: true` אומר שכל הודעה חייבת להשתמש באחת מהתבניות המוחזרות.‬

‫ה-Desk נקרא מטוקן ה-Bearer.‬

## ‫נקודת קצה‬

| | |
| - | - |
| ‫**מתודה**‬ | `GET` |
| ‫**נתיב**‬ | `/api/halloapp/whatsapp/templates` |
| ‫**אימות**‬ | ‫טוקן Bearer‬ |

## ‫דוגמת בקשה‬

```http
GET /api/halloapp/whatsapp/templates HTTP/1.1
Host: app.halloapp.co.il
Authorization: Bearer <access_token>
```

## ‫דוגמת תשובה‬

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

### ‫שדות התשובה‬

| ‫שדה‬ | ‫טיפוס‬ | ‫תיאור‬ |
| --- | --- | --- |
| `isInforu` | boolean | ‫`true` = ה-Desk חייב לשלוח תבניות; `false` = טקסט חופשי.‬ |
| `templates[].id` | integer | ‫הערך שיש להעביר כ-`templateId` ב[שליחה](../sending/send-message.md).‬ |
| `templates[].inforuTemplateId` | integer | ‫מזהה התבנית בצד הספק.‬ |
| `templates[].nameHe` / `nameEn` | string | ‫שמות קריאים לאדם.‬ |
| `templates[].messageText` | string | ‫גוף התבנית, עם מצייני `[#n#]`.‬ |
| `templates[].language` | integer | ‫`1` = עברית, `2` = אנגלית.‬ |
| `templates[].category` | integer | ‫`1` = שירות/תועלת, `2` = שיווק.‬ |
| `templates[].parameterCount` | integer | ‫המספר המדויק של ערכי `templateParams` שהתבנית מצפה להם.‬ |

## ‫שגיאות‬

| ‫מצב‬ | ‫תוצאה‬ |
| --- | --- |
| ‫טוקן חסר / פקוע‬ | HTTP 401 |
| ‫Desk לא נמצא לטוקן‬ | HTTP 200, `{ "ok": false, "msg": "desk not found for token" }` |

## ‫נסו את זה‬

{% openapi-operation spec="halloapp-api" path="/api/halloapp/whatsapp/templates" method="get" %}
{% endopenapi-operation %}
