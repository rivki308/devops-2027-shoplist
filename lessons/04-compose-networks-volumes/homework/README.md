1. ל-backend אין ports. איך stats מגיע אליו? ואיך הדפדפן מגיע אליו?

Stats מגיע ל-backend דרך רשת Docker באמצעות שם השירות backend והפורט הפנימי שלו. הדפדפן מגיע אליו דרך Nginx, שמעביר את הבקשות ל-backend באמצעות Reverse Proxy.

2. עושים docker compose down ואחר כך up. המוצרים נשארו? ומה אם עושים down -v?

כן. המוצרים נשמרים כי הם מאוחסנים ב-Named Volume, שנשאר גם לאחר down. לעומת זאת, down -v מוחק גם את ה-Named Volume ולכן המוצרים נמחקים.

3. השורה ../sql/init.sql:/docker-entrypoint-initdb.d/init.sql: איזה סוג Volume זה, ואיך יודעים? מה יקרה אם תשנו את init.sql אחרי שהמסד כבר נוצר?

זה Bind Mount, כי מחברים קובץ מהמחשב (../sql/init.sql) לנתיב בתוך הקונטיינר. שינוי הקובץ לא יריץ מחדש את פקודות האתחול, כי הן מופעלות רק ביצירת מסד נתונים חדש.

4. מה שם הרשת ש-Compose יצר, ומה שם ה-Volume?

Compose יוצר שמות לפי שם הפרויקט, בדרך כלל שם התיקייה. הרשת נקראת בדרך כלל devops-2027-shoplist_default, וה-Volume מקבל את שם הפרויקט בתוספת שם ה-Volume שהוגדר בקובץ Compose. יש לוודא את השמות המדויקים באמצעות docker network ls ו-docker volume ls.

5. ב-Dockerfile.multistage: מה יש ב-image הסופי ומה אין? למה אין CMD? ולמה ל-stats לא צריך multi-stage?

ב-image הסופי יש Nginx וקובצי האתר המוכנים, אך אין Node.js וכלי הבנייה. אין צורך ב-CMD כי image הבסיס של Nginx כבר מגדיר פקודת הפעלה. ל-stats לא צריך multi-stage כי אין בו שלב בנייה נפרד שמצריך כלי פיתוח.

6. שלושת הגדלים מ-ד8 (single, multi, kitchen). למה multi כמעט בגודל של single?

כי גם ב-image הסופי של multi נשאר Nginx, שתופס את רוב הנפח. קובצי האתר המוכנים קטנים יחסית, ולכן החיסכון אינו גדול.

7. ב-nginx.conf: בקשה ל-/api/stats מתאימה גם ל-location /api/. למה היא מגיעה ל-stats? ומה קורה לבקשה /api/products בגלל הלוכסן בסוף http://backend:5000/?

הבקשה /api/stats מגיעה ל-stats בגלל כלל ניתוב ספציפי יותר שמוגדר עבורה ב-Nginx. בבקשה /api/products, הלוכסן בסוף כתובת ה-Backend גורם ל-Nginx להסיר את הקידומת /api/, ולכן הבקשה מועברת כ-/products.
