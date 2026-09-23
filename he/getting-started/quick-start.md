# ‫התחלה מהירה‬

‫שלחו את הודעת ה-WhatsApp הראשונה שלכם בארבעה צעדים. עבדו מול **סביבת ה-QA** (`https://appqa.halloapp.co.il`) עד שהתהליך שלכם יציב.‬

### ‫1. השיגו טוקן Bearer‬

‫החליפו את אישורי ה-Desk בטוקן קצר-מועד (תקף כ-4 דקות, ניתן לשימוש חוזר עד לפקיעה).‬

```http
POST /api/halloapp/token HTTP/1.1
Host: appqa.halloapp.co.il
Content-Type: application/json

{
  "client_id": "11111111-2222-3333-4444-555555555555",
  "client_secret": "your-desk-api-secret"
}
```

‫תשובה:‬

```json
{ "ok": true, "access_token": "eyJhbGciOiJIUzI1NiIs...", "token_type": "Bearer", "expires_in": 240 }
```

‫שלחו את `access_token` כ-`Authorization: Bearer <access_token>` בכל קריאה למטה. ראו [סקירת אימות](../authentication/overview.md).‬

### ‫2. גלו כיצד ה-Desk שלכם שולח‬

‫בררו באיזה מצב ספק ה-Desk עובד. `isInforu: true` אומר שאתם חייבים לשלוח תבניות מאושרות; `isInforu: false` אומר טקסט חופשי.‬

```http
GET /api/halloapp/whatsapp/templates HTTP/1.1
Host: appqa.halloapp.co.il
Authorization: Bearer <access_token>
```

‫ראו [רשימת תבניות מאושרות](../discovery/templates.md).‬

### ‫3. שלחו הודעה‬

‫**Desk מסוג Green API (טקסט חופשי):**‬

```http
POST /api/halloapp/whatsapp/send HTTP/1.1
Host: appqa.halloapp.co.il
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "phone": "0501234567",
  "name": "Israel Israeli",
  "message": "Hello! Your document is ready:",
  "fileUrl": "https://myapp.example/reports/vat-2026-06.pdf"
}
```

‫**Desk מסוג InforU (תבנית מאושרת):**‬

```http
POST /api/halloapp/whatsapp/send HTTP/1.1
Host: appqa.halloapp.co.il
Content-Type: application/json
Authorization: Bearer <access_token>

{
  "phone": "0501234567",
  "name": "Israel Israeli",
  "templateId": 17,
  "templateParams": ["Israel Israeli", "VAT report", "2026-06", "My Business Ltd", "https://myapp.example/reports/vat-2026-06.pdf"]
}
```

‫ראו [שליחת הודעת WhatsApp](../sending/send-message.md).‬

### ‫4. בדקו את התשובה‬

```json
{ "ok": true, "chatId": 92, "messageId": 4587, "status": 0 }
```

‫`ok: true` עם `status >= 0` אומר שהספק קיבל את ההודעה. בשגיאה עסקית תקבלו `ok: false` ו-`msg` — ראו את קטלוג השגיאות ב[ברוכים הבאים](welcome.md#response--error-model).‬

### ‫מה הלאה‬

* ‫קראו את [הטיפים והכללים](key-tips.md) לפני עלייה לאוויר.‬
* ‫רוצים לשייך הודעות לנציג מסוים? ראו [רשימת נציגי ה-Desk](../discovery/agents.md).‬
