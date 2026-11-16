# שרת Web: Nginx בגרסה קלה (alpine)
FROM nginx:1.27-alpine

# הגדרות Nginx שלנו (כולל העברת /storage/ לשרת האחסון)
COPY nginx.conf /etc/nginx/conf.d/default.conf

# קובץ לוח הבקרה
COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
