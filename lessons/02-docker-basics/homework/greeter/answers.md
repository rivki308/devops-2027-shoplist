# תשובות — שיעורי בית Docker

## 1. בניית ה-image

```shell
docker build -t greeter:v1 lessons/02-docker-basics/homework/greeter
```

## 2. הרצת הקונטיינר ברקע

```shell
docker run -d -p 9000:8000 --name greeter greeter:v1
```

## 3. שינוי הברכה ל-Shalom

```shell
docker run -d -p 9000:8000 --name greeter -e GREETING=Shalom greeter:v1
```

## 4. התוצאה הצפויה

```json
{"message":"Shalom, Rivka!"}
```

## 5. שימוש ב-cache לאחר שינוי app.py

שלבי `FROM`, `WORKDIR`, `COPY requirements.txt` ו-`RUN pip install` יכולים להיות מסומנים `CACHED`, אם לא השתנו ההגדרות או קובץ התלויות.

שלב `COPY app.py` ירוץ מחדש, כי תוכן הקובץ השתנה. גם שלבים שבאים אחריו, אם יהיו, עשויים לרוץ מחדש.

הסיבה היא ש-Docker משתמש בתוצאות קודמות של שלבים שלא השתנו, וכך חוסך זמן בבנייה מחדש.
