# ‫סקירת אימות‬

‫ה-HalloApp Send API משתמש ב-**client credentials בסגנון OAuth**. אתם מחליפים את אישורי ה-Desk בטוקן Bearer קצר-מועד, ואז שולחים את הטוקן הזה בכל קריאה אחרת.‬

### ‫אישורים‬

‫HalloApp מנפיקה שני ערכים מחוץ למערכת:‬

| ‫ערך‬ | ‫משמעות‬ |
| --- | --- |
| `client_id` | ‫ה-**GUID של ה-Desk** שלכם.‬ |
| `client_secret` | ‫**סוד ה-API** של ה-Desk.‬ |

### ‫זרימת הטוקן‬

1. ‫`POST /api/halloapp/token` עם `client_id` + `client_secret`.‬
2. ‫מקבלים `access_token` (JWT חתום) תקף ל-**240 שניות (כ-4 דקות)**, ניתן לשימוש חוזר עד לפקיעה.‬
3. ‫שולחים אותו כ-`Authorization: Bearer <access_token>` בכל נקודת קצה אחרת.‬

‫הטוקן משויך ל-Desk שהנפיק אותו, ולכן נקודות הקצה של שליחה/גילוי אף פעם לא מקבלות `deskGuid` — ה-Desk נקרא מהטוקן.‬

## ‫נקודת קצה‬

| | |
| - | - |
| ‫**מתודה**‬ | `POST` |
| ‫**נתיב**‬ | `/api/halloapp/token` |
| ‫**אימות**‬ | ‫ללא (זו האתחול שמנפיק את הטוקן)‬ |

## ‫סכימת הבקשה‬

| ‫שדה‬ | ‫טיפוס‬ | ‫חובה‬ | ‫תיאור‬ |
| ----- | ---- | -------- | ----------- |
| `client_id` | string | ‫כן‬ | ‫ה-GUID של ה-Desk שלכם.‬ |
| `client_secret` | string | ‫כן‬ | ‫סוד ה-API של ה-Desk.‬ |

## ‫דוגמת בקשה‬

```http
POST /api/halloapp/token HTTP/1.1
Host: app.halloapp.co.il
Content-Type: application/json

{
  "client_id": "11111111-2222-3333-4444-555555555555",
  "client_secret": "your-desk-api-secret"
}
```

## ‫דוגמת תשובה‬

```json
{
  "ok": true,
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 240
}
```

## ‫שגיאות‬

| ‫מצב‬ | ‫תוצאה‬ |
| --- | --- |
| ‫חסר `client_id` / `client_secret`‬ | HTTP 200, `{ "ok": false, "msg": "client_id and client_secret are required" }` |
| ‫`client_id` / `client_secret` שגויים‬ | HTTP 401 Unauthorized |
| ‫Desk לא נמצא למזהה‬ | HTTP 200, `{ "ok": false, "msg": "desk not found for the supplied client_id" }` |

## ‫נסו את זה‬

{% openapi-operation spec="halloapp-api" path="/api/halloapp/token" method="post" %}
{% endopenapi-operation %}
