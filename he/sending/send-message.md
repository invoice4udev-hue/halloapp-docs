# ‫שליחת הודעת WhatsApp‬

‫שולח הודעת WhatsApp למספר טלפון דרך ה-Desk של הטוקן. ההודעה נשמרת כשיחה ב-HalloApp כך שתשובות משורשרות חזרה לנציגי ה-Desk.‬

‫ה-Desk נקרא מטוקן ה-Bearer — אין פרמטר `deskGuid`.‬

## ‫נקודת קצה‬

| | |
| - | - |
| ‫**מתודה**‬ | `POST` |
| ‫**נתיב**‬ | `/api/halloapp/whatsapp/send` |
| ‫**אימות**‬ | ‫טוקן Bearer‬ |

## ‫כללי ספק‬

| ‫ספק ה-Desk‬ | `message` | `templateId` + `templateParams` | `fileUrl` |
| --- | --- | --- | --- |
| **InforU** | ‫מתעלמים (התבנית מגדירה את הטקסט)‬ | ‫**חובה**; מספר הפרמטרים חייב להתאים ל-`parameterCount`‬ | ‫שימו את הכתובת **בתוך** `templateParams`‬ |
| **Green API** | ‫חובה (או `fileUrl`)‬ | ‫נדחה‬ | ‫מצורף לטקסט כקישור‬ |

‫גלו באיזה מצב ה-Desk שלכם עובד דרך [רשימת תבניות](../discovery/templates.md) (`isInforu`).‬

## ‫סכימת הבקשה‬

| ‫שדה‬ | ‫טיפוס‬ | ‫חובה‬ | ‫תיאור‬ |
| ----- | ---- | -------- | ----------- |
| `phone` | string | ‫כן‬ | ‫פורמט מקומי (`05...`) או בין-לאומי.‬ |
| `name` | string | ‫לא‬ | ‫בשימוש כשנוצרים צ'אט/איש קשר חדשים.‬ |
| `agentId` | integer | ‫לא‬ | ‫שיוך ההודעה לנציג ה-Desk הזה; מושמט = הודעת מערכת.‬ |
| `message` | string | ‫מותנה‬ | ‫Desk מסוג Green API: טקסט ההודעה.‬ |
| `templateId` | integer | ‫מותנה‬ | ‫Desk מסוג InforU: **חובה** (מ[תבניות](../discovery/templates.md)).‬ |
| `templateParams` | string[] | ‫מותנה‬ | ‫ערך אחד לכל מציין `[#n#]`, לפי הסדר.‬ |
| `fileUrl` | string | ‫לא‬ | ‫קישור למסמך (ראו כללי ספק).‬ |

## ‫דוגמת בקשה — טקסט חופשי (Desk מסוג Green API)‬

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

## ‫דוגמת בקשה — תבנית (Desk מסוג InforU)‬

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

## ‫דוגמת תשובה‬

```json
{
  "ok": true,
  "chatId": 92,
  "messageId": 4587,
  "status": 0
}
```

### ‫שדות התשובה‬

| ‫שדה‬ | ‫טיפוס‬ | ‫תיאור‬ |
| --- | --- | --- |
| `ok` | boolean | ‫`true` בהצלחה; `false` בשגיאה עסקית (עם `msg`).‬ |
| `chatId` | integer | ‫מזהה השיחה ב-HalloApp (נוצר אם לא היה קיים).‬ |
| `messageId` | integer | ‫מזהה רשומת ההודעה ב-HalloApp.‬ |
| `status` | integer | ‫סטטוס הספק; `>= 0` אומר שהספק קיבל את ההודעה.‬ |
| `msg` | string | ‫קיים רק כאשר `ok: false` — ראו שגיאות למטה.‬ |

## ‫שגיאות‬

‫שגיאות עסקיות מחזירות HTTP 200 עם `ok: false` ו-`msg`:‬

| `msg` | ‫סיבה / תיקון‬ |
| --- | --- |
| `phone is required` / `invalid phone` | ‫ספקו מספר טלפון תקין.‬ |
| `agent not found in this desk` | ‫`agentId` חייב להיות חבר פעיל ב-Desk של הטוקן.‬ |
| `this desk requires a templateId (see GET /api/halloapp/whatsapp/templates)` | ‫Desk מסוג InforU — שלחו `templateId`.‬ |
| `this desk does not support templates - send free text instead` | ‫Desk מסוג Green API — השמיטו `templateId`, שלחו `message`.‬ |
| `message or fileUrl is required` | ‫שליחת טקסט חופשי דורשת לפחות `message` או `fileUrl`.‬ |
| `template expects N parameter(s), got M` | ‫מספר ה-`templateParams` חייב להיות שווה למספר מצייני `[#n#]` של התבנית.‬ |
| `template not found for this desk` | ‫ה-`templateId` אינו שייך ל-Desk הזה.‬ |
| `on this desk files are delivered as a link inside a template parameter - include fileUrl as one of templateParams` | ‫Desk מסוג InforU — העבירו `fileUrl` כאחד מ-`templateParams`.‬ |

‫טוקן חסר או פקוע מחזיר **HTTP 401**.‬

## ‫נסו את זה‬

{% openapi-operation spec="halloapp-api" path="/api/halloapp/whatsapp/send" method="post" %}
{% endopenapi-operation %}
