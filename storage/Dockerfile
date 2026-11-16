# שרת אחסון תמונות: Apache (httpd) בגרסה קלה (alpine)
FROM httpd:2.4-alpine

# מעתיקים את תמונות הלוחיות ההתחלתיות לתיקייה שApache מגיש.
# ב-Apache ברירת המחדל מציגה רשימת קבצים של תיקייה (Directory Listing) -
# זה מה שהדאשבורד קורא כדי לדעת אילו תמונות קיימות.
COPY plates/ /usr/local/apache2/htdocs/plates/

EXPOSE 80
