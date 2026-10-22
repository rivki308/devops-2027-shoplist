
# בוחרים image בסיס של Python בגרסה מדויקת
FROM python:3.12.7-slim

# מגדירים את תיקיית העבודה בתוך ה-image
WORKDIR /app

# מעתיקים את קובץ התלויות לפני העתקת הקוד כדי לאפשר שימוש ב-cache
COPY requirements.txt .

# מתקינים את התלויות הדרושות לסרוויס
RUN pip install --no-cache-dir -r requirements.txt

# מעתיקים את קוד האפליקציה לתוך תיקיית העבודה
COPY app.py .

# מציינים שהשרת מאזין בתוך הקונטיינר בפורט 8000
EXPOSE 8000

# מפעילים את שרת Flask באמצעות תחביר exec
CMD ["python", "app.py"]
