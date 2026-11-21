# שיעורי בית 6 — Kubernetes

## 1. שמות Images ו־imagePullPolicy
השתמשתי בשמות Images שהגדרתי בעצמי, כמו `shoplist-backend:local`, כדי לוודא שה־Deployments משתמשים בתמונות שבניתי. ללא `imagePullPolicy: Never`, Kubernetes עשוי לנסות למשוך את התמונה ממאגר חיצוני במקום להשתמש בתמונה המקומית שייבאתי ל־containerd.

## 2. פענוח Secret
הרצתי:
`kubectl get secret postgres-secret -n shoplist -o jsonpath="{.data.POSTGRES_PASSWORD}"`

הערך שהתקבל היה `c2hvcHBhc3M=`, ולאחר פענוח Base64 מתקבלת הסיסמה `shoppass`. למדתי ש־Base64 הוא קידוד ולא הצפנה, ולכן Secret אינו מוגן רק מעצם הקידוד שלו.

## 3. שמירת נתוני PostgreSQL
הקובץ `postgres-pvc.yml` מגדיר PersistentVolumeClaim. בקובץ `postgres-deployment.yml` ה־PVC מחובר לנתיב הנתונים של PostgreSQL באמצעות `claimName: postgres-pvc` ו־`mountPath: /var/lib/postgresql/data`. כך הנתונים נשמרים גם כאשר ה־Pod נמחק ונוצר מחדש.

## 4. סדר הפעלת השירותים
Kubernetes לא משתמש ב־`depends_on` כמו Docker Compose. ה־Backend יכול להתחיל לפני PostgreSQL, ולכן עליו להתמודד עם מצב שבו מסד הנתונים עדיין אינו זמין. `readinessProbe` בודק את הנתיב `/health` בפורט 5000, ורק כשהבדיקה מצליחה ה־Pod מסומן כ־Ready ומקבל תעבורה דרך ה־Service.

## 5. בדיקת Service Selector
כאשר משנים את ה־selector של שירות ה־Backend מ־`app: backend` ל־`app: backnd`, הוא אינו תואם לתוויות של ה־Backend Pods. ה־Pods עצמם ממשיכים לפעול, אך ה־Service אינו מוצא אותם ולכן `kubectl get endpoints backend -n shoplist` מציג `<none>`. כתוצאה מכך בקשות דרך השירות אינן מגיעות ל־Backend. לאחר החזרת הערך ל־`app: backend`, ה־Endpoints חוזרים להופיע.

## 6. עדכון ConfigMap
עדכון ConfigMap אינו משנה אוטומטית משתני סביבה שכבר נטענו לתוך Containers פעילים. כדי שהאפליקציה תטען את הערכים החדשים צריך להפעיל מחדש את ה־Deployment באמצעות `kubectl rollout restart`, וכך נוצרים Pods חדשים עם הערכים המעודכנים.

## 7. שימוש ב־namespace
כתבתי את `shoplist` בשדה `namespace` ב־12 קובצי YAML. אם ארצה להפעיל עותק נוסף של ShopList ב־namespace בשם `shoplist-test`, אצטרך לשנות את הערך בכל 12 הקבצים. אפשר להימנע מחזרה ידנית באמצעות Kustomize, שמאפשר להגדיר namespace במקום מרכזי.
