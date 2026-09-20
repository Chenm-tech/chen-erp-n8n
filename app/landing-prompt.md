# Lovable — דף נחיתה (דמו) לחנות, מחובר ל-WF2

פרויקט Lovable **נפרד** מאפליקציית הניהול (כדי לא לגעת בה). לפני ההדבקה מחליפים `LEAD_WEBHOOK_URL`
בכתובת ה-Production של WF2 (`https://<instance>/webhook/lead`). הכתובת נשארת מחוץ לריפו.

## פרומפט (להדביק כמו שהוא)

```
Build a single-page marketing landing page for "Chen Electronics" (חן אלקטרוניקה), a small Israeli
electronics store, promoting its wireless headphones. Hebrew UI, RTL (dir="rtl"), ILS prices (₪),
mobile-friendly, clean light design with a blue accent (#1f4e79). No database, no auth.

Sections:
1. Hero: headline "אוזניות אלחוטיות עם סינון רעשים, במחיר של חנות שכונתית", short subtitle, CTA button
   "קבלו הצעת מחיר" that scrolls to the form.
2. Three product cards (name, 2-3 bullet specs, price): 
   - אוזניות אלחוטיות TY-200 — ANC עד 32dB, עד 30 שעות עם ANC, 349 ₪
   - אוזניות תוך-אוזן TY-Buds Pro — ANC + מצב שקיפות, 8 שעות + 32 עם נרתיק, 289 ₪
   - אוזניות גיימינג TY-Gamer H7 — מיקרופון עם סינון רעשים, 429 ₪
3. "למה אצלנו": 14 ימי החזרה, אחריות יבואן שנה, משלוח חינם מעל 299 ₪, תמיכה בטלגרם.
4. Lead form (the only interactive part): fields Name (required), Email (required), Phone, Company
   (optional), and a select "מה מעניין אותך?" with options: אוזניות TY-200, אוזניות TY-Buds Pro,
   אוזניות TY-Gamer H7, ייעוץ כללי. Submit button "שלחו לי הצעה".
   On submit, POST JSON to the constant LEAD_WEBHOOK_URL = "LEAD_WEBHOOK_URL" with body
   { "name", "email", "phone", "company", "interest" } (interest = the selected option text).
   Response { ok: true, created: true } → replace the form with "תודה! נציג יחזור אליכם במייל בקרוב."
   Response { ok: true, created: false, reason: "duplicate" } → "הפרטים כבר אצלנו, ניצור קשר בקרוב."
   HTTP 400 or network error → red toast "משהו השתבש, נסו שוב".
   Disable the button while sending. No other network calls.
5. Footer: חן אלקטרוניקה · רחוב הברזל 30, תל אביב · 03-555-1234 · support@chen-electronics.co.il

Keep the code simple: one page, one fetch call.
```

## מה קורה אחרי שליחה

הטופס → WF2 (`/webhook/lead`): בדיקת אימייל → סינון כפילות → רשומה ב-Leads בסטטוס New → מייל התראה
לבעלים → הליד מופיע באפליקציית הניהול → WF3 שולח לו מייל פתיחה בריצה הבאה (כל 3 שעות).

## הערה

כתובת ה-webhook נמצאת בקוד הדף (ציבורי, כמו באפליקציית הניהול). WF2 עצמו מגן: בלי אימייל → 400,
אימייל שכבר קיים → לא נוצר כפול. זה דף דמו; בפרודקשן מוסיפים CAPTCHA או header סודי.
