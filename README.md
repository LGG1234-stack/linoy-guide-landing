# דף נחיתה — "להפסיק גלולות בלי לפחד מהעור"

דף מכירה למדריך הדיגיטלי של לינוי לוי קליניק, במודל טריפוויר.
המטרה: מכירת המדריך, ואחרי הרכישה הפניה לתיאום אבחון בקליניקה.

## קבצים
- `index.html` — דף הנחיתה המלא (9 סקשנים, RTL, מובייל פירסט).
- `thank-you.html` — עמוד תודה אחרי רכישה, עם כפתור וואטסאפ לתיאום אבחון.
- `og-image.png` — תמונת שיתוף ממותגת מוכנה (1200x630) לרשתות החברתיות.
- `og-image.html` — המקור לעיצוב תמונת השיתוף (לעריכה וייצוא מחדש, ראי למטה).

עמודי ה-HTML הם בקובץ אחד כל אחד (HTML + CSS + JS), בלי תלויות. אפשר לפתוח ישירות בדפדפן.

## איך מחליפים את ה-Placeholders

כל ההגדרות שצריך לערוך נמצאות בראש בלוק ה-`<script>` בתחתית `index.html`:

```js
const PAYMENT_URL = "PAYMENT_URL_PLACEHOLDER";  // קישור התשלום (placeholder)
const SUCCESS_URL = "thank-you.html";           // עמוד הצלחה אחרי תשלום
const PRICE_NOW   = "49";   // מחיר השקה
const PRICE_OLD   = "99";   // מחיר מקורי (עוגן, מוצג מחוק)
```

1. **קישור תשלום** — החליפי את `PAYMENT_URL` בכתובת מספק הסליקה (Grow / iCount / PayPlus / Stripe).
   כל כפתורי הרכישה בדף (Hero, הצעה, סגירה, וסרגל צף) מצביעים אוטומטית לכתובת הזו.
2. **מחיר** — עדכני את `PRICE_NOW` ו-`PRICE_OLD`. הם מוזרקים אוטומטית לקופסת המחיר.
3. **תמונה של לינוי** — בסקשן "מי זאת לינוי" יש מסגרת עם placeholder (ראשי תיבות "ל״ל").
   חפשי את ההערה `<!-- TODO: <img ...` והחליפי את ה-`<span class="initials">` בתגית `<img src="linoy.jpg" alt="לינוי לוי">`.
4. **פיקסל פרסום ואנליטיקס** — חפשי `META PIXEL CODE GOES HERE` ו-`GOOGLE ANALYTICS CODE GOES HERE` ב-`<head>`.
   לאירוע רכישה: בעמוד `thank-you.html` יש מקום מסומן `META PIXEL PURCHASE EVENT`.
5. **כתובת השיתוף (Open Graph)** — `og-image.png` כבר מוכנה ומחוברת בשני העמודים.
   צריך רק להחליף `BASE_URL` בכתובת הדומיין המלאה (רשתות חברתיות דורשות כתובת מלאה, לא יחסית).
   חפשי `BASE_URL` ב-`index.html` וב-`thank-you.html`.

## תשלום ומסירת הקובץ — מבנה מוכן לחיבור

ספק סליקה עדיין לא נסגר, אז `PAYMENT_URL` נשאר placeholder. שלוש נקודות החיבור מסומנות
בבירור בקוד (חפשי "נקודת חיבור" בתחתית `index.html`):

1. **קישור תשלום** — `PAYMENT_URL`. מחרוזת אחת, כל הכפתורים מתעדכנים יחד.
2. **Redirect אחרי תשלום** — `SUCCESS_URL` (מצביע ל-`thank-you.html`).
   זו הכתובת שתיתני לספק הסליקה כ"כתובת הצלחה".
3. **Webhook למסירת ה-PDF** — מוגדר בלוח הבקרה של ספק הסליקה (לא בקוד):
   תשלום מוצלח → Webhook ל-Make / Zapier → מייל עם קישור להורדת ה-PDF.

## עריכת תמונת השיתוף (OG)

העיצוב נמצא ב-`og-image.html`. לעריכה ולייצוא מחדש ל-PNG:

```bash
cd landing
# ערכי את og-image.html, ואז ייצאי מחדש עם Chrome (macOS):
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu --hide-scrollbars --force-device-scale-factor=1 \
  --window-size=1200,630 --screenshot="og-image.png" "file://$(pwd)/og-image.html"
```

## הרצה מקומית

פותחים את `index.html` בדפדפן, או מריצים שרת סטטי:

```bash
cd landing
python3 -m http.server 8899
# גלשי אל http://localhost:8899/index.html
```

## אירוח — Netlify + guide.linoyclinic.com

הדף סטטי לחלוטין, מאורח ב-Netlify (חינמי) תחת הכתובת **https://guide.linoyclinic.com**.
כל קובצי ה-OG, ה-`SUCCESS_URL`, וה-sitemap כבר מוגדרים לכתובת הזו.

### שלב 1 — העלאת האתר (drag and drop)
1. היכנסי ל-https://app.netlify.com/drop (אפשר עם חשבון גוגל).
2. גררי את **כל תיקיית `landing`** אל החלון. האתר עולה תוך שניות וקבלת כתובת זמנית (לדוגמה `xxx.netlify.app`).
3. לעדכון עתידי: גררי שוב את התיקייה לאותו אתר (Deploys → drag and drop), או חברי ל-GitHub לפריסה אוטומטית.

> קובץ `netlify.toml` שבתיקייה נקרא אוטומטית ומגדיר כותרות אבטחה וקאשינג. אין צורך לגעת בו.

### שלב 2 — חיבור הדומיין guide.linoyclinic.com
1. ב-Netlify: **Domain settings → Add a domain** → הקלידי `guide.linoyclinic.com`.
2. Netlify ייתן לך רשומת DNS להוסיף אצל מי שמנהל את הדומיין `linoyclinic.com`:
   - סוג: **CNAME**
   - שם / host: `guide`
   - ערך / target: הכתובת ש-Netlify מציג (בדרך כלל `xxx.netlify.app`)
3. הוסיפי את הרשומה בלוח ה-DNS של הדומיין. ההפעלה לוקחת בין כמה דקות לכמה שעות.
4. Netlify מנפיק תעודת HTTPS אוטומטית (Let's Encrypt) ברגע שה-DNS מתעדכן.

### שלב 3 — בדיקת תצוגת השיתוף
אחרי שהאתר באוויר, בדקי איך הקישור נראה בשיתוף:
- פייסבוק / אינסטגרם: https://developers.facebook.com/tools/debug/ (הדביקי את הכתובת ולחצי Scrape Again).
- וואטסאפ: פשוט שלחי את הקישור לעצמך, התצוגה המקדימה אמורה להופיע עם התמונה הממותגת.
