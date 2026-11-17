# 🛸 חניון חלליות חכם - Smart Parking License Plate Tracker

מסלול **Track 1 - Lite**: לוח בקרה לשומר החניון שמציג את תמונות לוחיות הרישוי שהמצלמה צילמה.
הנושא שלנו: **חניון לחלליות** (לוחיות כמו `ZORG-42`).

**הצוות:** [דויד] ו-[ניקיטה]

## ארכיטקטורה

```
דפדפן ──► http://localhost:8080
              │
        ┌─────▼──────┐   /storage/*    ┌──────────────┐
        │ web (Nginx)│ ───────────────►│ storage      │
        │ index.html │  רשת פנימית של  │ (Apache httpd)│
        └────────────┘  Docker Compose └──────┬───────┘
                                              │
                                    Volume: plates_data
                                  (תמונות הלוחיות נשמרות כאן)
```

- **web** - Nginx. מגיש את `index.html` ומעביר (Reverse Proxy) בקשות של `/storage/` לשרת האחסון.
- **storage** - Apache. שרת אחסון בלבד: מגיש את התמונות. לא חשוף החוצה - רק Nginx מדבר איתו.
- **Volume `plates_data`** - התמונות שנשמרות בתיקיית התמונות של Apache שורדות `docker compose down`.

## הרצה

דרישות: Docker Desktop (או Docker Engine + Compose).

```bash
docker compose up --build
```

ואז לפתוח את http://localhost:8080 . לעצירה: `docker compose down`.

## האלמנטים האישיים שלנו

1. **נושא חלליות** - עיצוב חלל, שמות לוחיות, ולכל חללית הודעה מצחיקה.
2. **התראת "חניון מלא"** - כשיש 8 חלליות (`CAPACITY` ב-`web/index.html`) מופיע באנר אדום מהבהב והמונה הופך לאדום.
3. **רענון חי + הודעת כניסה** - הדף שואל את שרת האחסון כל 3 שניות. כשנכנסת חללית חדשה קופצת הודעה "🚀 נכנסה חללית".
4. **Easter egg** - 5 לחיצות על 🛸 בכותרת משגרות חללית על המסך.

## הדגמה: "המצלמה" מצלמת רכב חדש

בתיקיית `new-cars/` יש לוחיות נוספות. מעתיקים אחת לשרת האחסון, והיא מופיעה בדאשבורד תוך 3 שניות בלי רענון:

```bash
docker compose cp new-cars/PULSAR-11.png storage:/usr/local/apache2/htdocs/plates/
```

## הוכחת Persistence (Volume)

```bash
docker compose cp new-cars/PULSAR-11.png storage:/usr/local/apache2/htdocs/plates/
docker compose down          # מוחק קונטיינרים, אבל לא את ה-Volume
docker compose up -d
# ה-PULSAR-11 עדיין שם!

docker compose down -v       # ה-v מוחק גם את ה-Volume
docker compose up -d
# עכשיו חזרנו רק ל-5 החלליות ההתחלתיות
```

## CI/CD (GitHub Actions)

הקובץ `.github/workflows/ci.yml`:

- **build** - בכל push: `docker compose build`, אחר כך הרמת המערכת ובדיקה ש-Nginx עונה ושהתמונות מגיעות דרך ה-proxy.
- **publish** (בונוס) - ב-push ל-`main`, אחרי ש-build עבר: התחברות ל-`ghcr.io` עם `GITHUB_TOKEN`, תיוג אוטומטי (`v1.<מספר ריצה>`, `latest`, `sha`) ו-push של שני האימג'ים (web, storage).

## מבנה התיקיות

```
.
├── docker-compose.yml        # הגדרת השירותים וה-Volume
├── web/                      # Nginx
│   ├── Dockerfile
│   ├── nginx.conf
│   └── index.html            # לוח הבקרה
├── storage/                  # Apache
│   ├── Dockerfile
│   └── plates/               # תמונות התחלתיות
├── new-cars/                 # תמונות להדגמה
├── .github/workflows/ci.yml
├── AI_USAGE.md
└── DECISIONS.md
```

## חלוקת עבודה

| מי | מה |
|----|----|
| [דויד] | שרת storage (Apache + Volume), תמונות, AI_USAGE.md |
| [ניקיטה] | שרת web (Nginx + לוח הבקרה), GitHub Actions |
| שניהם | README, בדיקות, הכנה ל-Demo |
