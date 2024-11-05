# Docker - Laravel - PHP 8.2

Stack to Docker Swarm

## Ports

Ports used in the project:
| Software | Port |
|-------------- | -------------- |
| **nginx** | 8080 |

3. Build the project whit the next commands:

```sh
docker-compose up -d
```

4. Install Dependency:

```sh
docker-compose run --rm composer install && docker-compose run --rm npm install && docker-compose run --rm npm run prod
```

5. Generate Key:

```sh
docker-compose run --rm artisan key:generate && docker-compose run --rm artisan optimize
```

6. Run Migrate and Seed:

```sh
docker-compose run --rm artisan migrate:fresh --seed
```

---

## Special Cases

To Down and remove the volumes we use the next command:

```sh
docker-compose down -v
```

Update Composer:

```sh
docker-compose run --rm composer update
```

Run compiler (Webpack.mix.js) or Show the view compiler in node:

```sh
docker-compose run --rm npm run dev
```

Run all migrations:

```sh
docker-compose run --rm artisan migrate
```
