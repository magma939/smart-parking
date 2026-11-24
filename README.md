# חניון חלליות חכם

**מגישים:** דויד ברז'יזיצקי, ניקיטה פרלוב

לוח בקרה לשומר בחניון חלליות חכם: שרת אחד מציג את האתר ושרת שני שומר ומגיש את תמונות הרכבים מתוך ווליום קבוע.

## מבנה המערכת
* **שרת תצוגה:** מציג את הדאשבורד ומעביר בקשות תמונה לשרת האחסון דרך רשת פנימית.
* **שרת אחסון:** מגיש את תמונות הלוחיות מתוך תיקייה המחוברת לווליום.
* **ווליום:** שומר את התמונות גם אחרי כיבוי והדלקה מחדש של המערכת.

## הוראות הרצה
```bash
docker compose up --build -d
docker compose cp sample-plates/. storage:/usr/local/apache2/htdocs/plates/
docker compose cp new-cars/BLAST-01.svg storage:/usr/local/apache2/htdocs/plates/
docker compose cp new-cars/COMET-99.svg storage:/usr/local/apache2/htdocs/plates/
docker compose down
docker compose up -d

שים לב: הרצת docker compose down בלי -v שומרת את התמונות בווליום. הוספת -v תמחק את הווליום ותתחיל מחדש.
