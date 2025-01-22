# Docker - Laravel

rm -rf /var/www/app/public && git clone https://<seu_token>@github.com/usuario/repositorio.git /var/www/app

rm -rf /var/www/app/public && git clone https://ghp_abc123456789xyz@github.com/meusuario/meurepositorio.git /var/www/app

chown www-data:www-data /var/www/app/storage && chown www-data:www-data /var/www/app/logs

cd /var/www/app && composer install && npm install && npm run build

cd /var/www/app && nano .env

cd /var/www/app && php artisan key:generate && php artisan migrate && php artisan optimize
