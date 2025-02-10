# Docker - Laravel

rm -rf /var/www/app/public && git clone https://<seu_token>@github.com/usuario/repositorio.git /var/www/app

rm -rf /var/www/app/public && git clone https://ghp_abc123456789xyz@github.com/meusuario/meurepositorio.git /var/www/app

chown www-data:www-data /var/www/app/storage && chown www-data:www-data /var/www/app/storage/logs && chown www-data:www-data /var/www/app/storage/framework

cd /var/www/app && composer install && npm install && npm run build

cp /var/www/app/.env.example /var/www/app/.env

cd /var/www/app && nano .env

cd /var/www/app && php artisan key:generate && php artisan migrate && php artisan optimize


---

Edite app/Providers/AppServiceProvider.php

```php
        use Illuminate\Support\Facades\URL;
```

```php
        if (config('app.env') === 'production') {
            URL::forceScheme('https');
        }
```
