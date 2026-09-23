# ‫ברוכים הבאים ל-HalloApp Send API‬

‫ה-HalloApp Send API מאפשר לאפליקציות שותפות לשלוח הודעות WhatsApp דרך Desk של HalloApp — מסמכים, דוחות, התראות ועוד — ישירות מהמערכת שלכם. ההודעות נשמרות כשיחות רגילות ב-HalloApp, כך שתשובות חוזרות ל-Desk ומטופלות על ידי הנציגים בממשק של HalloApp.‬

### ‫מושגים‬

* ‫**Desk** — סביבת העבודה של HalloApp המחוברת לארגון שלכם. זו הזהות של הקורא: כל אישור API וכל הודעה משויכים ל-Desk אחד.‬
* ‫**ספק (Provider)** — כל Desk שולח WhatsApp דרך ספק אחד, והכללים שונים:‬
  * ‫**Desk מסוג InforU** — הודעות **חייבות** לצאת כתבנית מאושרת מראש (`templateId` הוא חובה).‬
  * ‫**Desk מסוג Green API** — טקסט חופשי בלבד (תבניות נדחות).‬
* ‫**קבצים** — נמסרים תמיד כ**קישור הורדה**, אף פעם לא כצירוף WhatsApp מקורי. הקישור חייב להיות נגיש ללקוח הקצה.‬
* ‫**צ'אט (Chat)** — כל הודעה שנשלחת הופכת לשיחה ב-HalloApp. אם אין שיחה קיימת למספר היעד, היא נוצרת (יחד עם איש קשר בספר הטלפונים), ותשובות הלקוח משורשרות לאותה שיחה.‬

### ‫קבלת גישה‬

‫האישורים ניתנים על ידי HalloApp מחוץ למערכת. אתם מקבלים:‬

1. ‫**`client_id`** — ה-GUID של ה-Desk שלכם.‬
2. ‫**`client_secret`** — סוד ה-API של ה-Desk.‬

‫מחליפים אותם ב[נקודת הקצה של הטוקן](../authentication/overview.md) בטוקן קצר-מועד, ואז שולחים את הטוקן הזה בכל קריאה אחרת.‬

### ‫כתובות בסיס‬

| ‫סביבה‬ | ‫כתובת בסיס‬ |
| ----- | ---------- |
| ‫פרודקשן‬ | `https://app.halloapp.co.il` |
| ‫QA (בדיקות)‬ | `https://appqa.halloapp.co.il` |

‫כל נקודות הקצה בתיעוד הזה יחסיות לכתובות הבסיס האלה. פתחו ובדקו מול **QA** תחילה, ואז החליפו ל**פרודקשן**.‬

### ‫פורמט הבקשות‬

‫כל נקודת קצה נקראת עם גוף JSON ומחזירה JSON. נקודות קצה מאומתות מצפות לטוקן ב-header של `Authorization`:‬

```http
POST /api/halloapp/whatsapp/send HTTP/1.1
Host: appqa.halloapp.co.il
Content-Type: application/json
Authorization: Bearer <access_token>

{ "phone": "0501234567", "message": "Hello!" }
```

### ‫אימות‬

‫**Client credentials** בסגנון OAuth. מחליפים את ה-`client_id` / `client_secret` בכתובת `POST /api/halloapp/token` בטוקן Bearer תקף ל-**4 דקות וניתן לשימוש חוזר עד לפקיעתו**. שולחים אותו כ-`Authorization: Bearer <access_token>` בכל נקודת קצה אחרת. ראו [סקירת אימות](../authentication/overview.md).‬

### ‫מודל התשובות והשגיאות‬

‫קריאות מוצלחות מחזירות `ok: true` עם תוכן הפעולה. **שגיאות עסקיות מחזירות HTTP 200 עם `ok: false` ו-`msg`** שמסביר את התיקון (טוקן חסר מחזיר HTTP 401). תמיד בדקו את `ok` לפני שימוש בתוצאה.‬

| ‫`msg` מכיל‬ | ‫סיבה / תיקון‬ |
| --- | --- |
| `client_id and client_secret are required` | ‫חסרים אישורים ב-`/token`.‬ |
| `desk not found for token` | ‫הטוקן תקף אך ה-Desk הוסר/הועבר לארכיון.‬ |
| `phone is required` / `invalid phone` | ‫מספר טלפון חסר או ריק.‬ |
| `this desk requires a templateId` | ‫Desk מסוג InforU — שלחו עם `templateId` + `templateParams`.‬ |
| `this desk does not support templates` | ‫Desk מסוג Green API — שלחו טקסט חופשי במקום.‬ |
| `template expects N parameter(s)` | ‫מספר ה-`templateParams` חייב להתאים למצייני `[#n#]` של התבנית.‬ |
| `agent not found in this desk` | ‫ה-`agentId` אינו חבר פעיל ב-Desk של הטוקן.‬ |

### ‫צעדים ראשונים‬

‫עברו על [ההתחלה המהירה](quick-start.md), ואז קראו את [הטיפים והכללים](key-tips.md) לפני עלייה לאוויר.‬

### ‫משאבים קריאים למכונה‬

* ‫[מפרט OpenAPI 3.0 (JSON)](https://raw.githubusercontent.com/invoice4udev-hue/halloapp-docs/main/openapi/halloapp-openapi.json) — כל ממשק ה-API עבור מחוללי קוד, Postman וסוכני AI.‬
* ‫רינדור ReDoc גולמי של אותו מפרט מוגש גם על ידי האפליקציה בכתובת `/apidocs`.‬

### ‫תמיכה‬

‫לעזרה באינטגרציה, פנו לתמיכת HalloApp. צרפו לכל פנייה את נקודת הקצה שנקראה, גוף הבקשה, גוף התשובה והסביבה (QA/פרודקשן).‬

### ‫עמודים הבאים‬

* ‫[התחלה מהירה](quick-start.md)‬
* ‫[סקירת אימות](../authentication/overview.md)‬
* ‫[סקירת שליחה](../sending/overview.md)‬
