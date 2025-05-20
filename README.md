# Docker - Laravel
```sh
rm -rf /var/www/app/public && git clone https://<seu_token>@github.com/usuario/repositorio.git /var/www/app
```
## Exemplo
```sh
rm -rf /var/www/app/public && git clone https://ghp_abc123456789xyz@github.com/meusuario/meurepositorio.git /var/www/app
```
```sh
chown -R www-data:www-data /var/www/app/storage/* && chown -R www-data:www-data /var/www/app/bootstrap/cache
```
```sh
cd /var/www/app && composer install && npm install && npm run build
```
```sh
cp /var/www/app/.env.example /var/www/app/.env && nano .env
```
```sh
cd /var/www/app && php artisan key:generate && php artisan migrate && php artisan optimize && php artisan view:cache
```

## Por fim

Edite app/Providers/AppServiceProvider.php

```php
        use Illuminate\Support\Facades\URL;
```

```php
        if (config('app.env') === 'production') {
            URL::forceScheme('https');
        }
```
