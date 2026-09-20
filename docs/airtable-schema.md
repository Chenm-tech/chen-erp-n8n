# סכימת Airtable

Base אחד (למשל `Chen-ERP`). ארבע טבלאות. שמות השדות חייבים להיות **בדיוק** כמו כאן, כי ה-workflows
פונים אליהם בשם. `scripts/airtable_setup.py` יוצר הכל אוטומטית; הטבלה למטה היא לתיעוד ולבדיקה ידנית.

## Invoices

| שדה | סוג | הערות |
|---|---|---|
| InvoiceNumber | Single line text (primary) | ריק ביצירה. WF1 ממלא `INV-0001`, `INV-0002`… |
| CustomerId | Single line text | מפתח זר כטקסט, למשל `CUST-0001` |
| Amount | Number (2 decimals) | לפני מע"מ. חובה > 0 |
| VatAmount | Number (2 decimals) | WF1 מחשב |
| Total | Number (2 decimals) | WF1 מחשב |
| Status | Single line text | ריק → `Ready` → `Issued` → `Paid`. או `Invalid` |
| PdfUrl | URL | WF8 כותב קישור Drive |
| Created | Created time | WF1 קובע לפיו את שיעור המע"מ (18% מ-2025) |

## Leads

| שדה | סוג | הערות |
|---|---|---|
| Name | Single line text (primary) | |
| Email | Email | מפתח הכפילויות ב-WF2 וההתאמה ב-WF4 |
| Phone | Phone | |
| Company | Single line text | אופציונלי |
| Interest | Single line text | נכנס לפרומפט של המייל הקר |
| Status | Single line text | `New` → `Contacted` → `Replied`. או `EmailFailed` |
| Created | Created time | WF3 שולח לפי הישן ביותר |

## Products

| שדה | סוג | הערות |
|---|---|---|
| Name | Single line text (primary) | |
| Category | Single line text | |
| Price | Number (0 decimals) | ש"ח כולל מע"מ |
| Description | Long text | מק"ט, מפרט, מה באריזה, אחריות |
| InStock | Checkbox | |

## Tasks

| שדה | סוג | הערות |
|---|---|---|
| Title | Single line text (primary) | |
| Status | Single line text | `Open` / `Done` |
| Notes | Long text | |
| Created | Created time | |

## שתי מלכודות שהמפרט מזהיר מהן

1. `Created` חייב להיות מסוג *Created time*, לא Date רגיל. בלעדיו הטריגר של Airtable לא מזהה רשומות חדשות.
2. `Status` חייב להיות טקסט חופשי ולא *Single select*. אחרת כתיבת ערך שלא הוגדר מראש (`EmailFailed`) נכשלת.

## מגבלות API

Airtable מאפשר ~5 בקשות לשנייה ל-base. הסקריפטים והדשבורד מכבדים את זה. Free plan: 1,000 רשומות לבסיס, מספיק בהרבה לדמו.


## Customers / Orders (מסכי הניהול שהמפרט דורש)

| טבלה | שדות |
|---|---|
| Customers | CustomerId · Name · Email · Phone · Company · Notes · Created |
| Orders | OrderId · CustomerId · Product · Quantity · Total · Status (New / Shipped / Delivered) · Created |

ב-Invoices נוסף `DocType` (חשבונית מס / קבלה / חשבונית). WF1 ממלא 'חשבונית מס' כברירת מחדל, ו-WF8 מדפיס אותו בכותרת המסמך. סטטוס `UploadFailed` נכתב כשההעלאה לדרייב נכשלת.
