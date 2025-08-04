# Docker - Laravel

```sh
rm -rf /var/www/app/public && git clone https://<seu_token>@github.com/usuario/repositorio.git /var/www/app
```

## Exemplo

```sh
rm -rf /var/www/app/public && git clone https://ghp_abc123456789xyz@github.com/meusuario/meurepositorio.git /var/www/app
```

```sh
cd /var/www/app && composer install && npm install && npm run build
```

```sh
cp /var/www/app/.env.example /var/www/app/.env && nano .env
```

```sh
cd /var/www/app && php artisan key:generate && php artisan migrate && php artisan optimize
```

```sh
chown -R www-data:www-data /var/www/app/storage/* && chown -R www-data:www-data /var/www/app/bootstrap/cache
```

## Fazer Pull e Atualizar

```sh
cd /var/www/app && git pull && composer install && npm install && npm run build && php artisan migrate && php artisan optimize && chown -R www-data:www-data /var/www/app/storage/* && chown -R www-data:www-data /var/www/app/bootstrap/cache
```

## Por fim

Edite app/Providers/AppServiceProvider.php

```php
        use Illuminate\Support\Facades\URL;
```

```php
        if ($this->app->environment('production')) {
            URL::forceScheme('https');
            URL::forceRootUrl(config('app.url'));
        }

        Vite::prefetch(concurrency: 3);
```
