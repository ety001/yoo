# Yoo

A social platform for YOYOW.

## Installation

Development environment requirements :
- [Docker](https://www.docker.com) >= 17.06 CE
- [Docker Compose](https://docs.docker.com/compose/install/)

Setting up your development environment on your local machine :
```
$ git clone https://github.com/ety001/yoo.git
$ cd yoo
$ cp .env.example .env
$ docker-compose run --rm --no-deps yoo-server composer install
$ docker-compose run --rm --no-deps yoo-server php artisan key:generate
$ docker-compose run --rm --no-deps yoo-server php artisan vendor:publish --provider="Laravel\Horizon\HorizonServiceProvider"
$ docker-compose run --rm --no-deps yoo-server php artisan storage:link
$ docker run --rm -it -v $(pwd):/app -w /app node npm install
$ docker-compose up -d
```

Now you can access the application via [http://localhost:8088](http://localhost:8088).

**There is no need to run ```php artisan serve```. PHP is already running in a dedicated container.**

## Before starting
You need to run the migrations with the seeds :
```
$ docker-compose run --rm yoo-server php artisan migrate --seed
```

This will create a new user that you can use to sign in :
```
Email : ety001@test.com
Password : 123456
```

And then, compile the assets :
```
$ docker run --rm -it -v $(pwd):/app -w /app node npm run dev
```

Starting job for newsletter :
```
$ docker-compose run yoo-server php artisan tinker
> PrepareNewsletterSubscriptionEmail::dispatch();
```

## Useful commands
Seeding the database :
```
$ docker-compose run --rm yoo-server php artisan db:seed
```

Running tests :
```
$ docker-compose run --rm yoo-server ./vendor/bin/phpunit --stop-on-failure
```

Running php-cs-fixer :
```
$ docker-compose run --rm --no-deps yoo-server ./vendor/bin/php-cs-fixer fix --config=.php_cs --verbose --dry-run --diff
```

Generating backup :
```
$ docker-compose run --rm yoo-server php artisan backup:run
```

Generating fake data :
```
$ docker-compose run --rm yoo-server php artisan db:seed --class=DevDatabaseSeeder
```

Discover package
```
$ docker-compose run --rm --no-deps yoo-server php artisan package:discover
```

In development environnement, rebuild the database :
```
$ docker-compose run --rm yoo-server php artisan migrate:fresh --seed
```

## Accessing the API

Clients can access to the REST API. API requests require authentication via token. You can create a new token in your user profil.

Then, you can use this token either as url parameter or in Authorization header :

```
# Url parameter
GET http://laravel-blog.app/api/v1/posts?api_token=your_private_token_here

# Authorization Header
curl --header "Authorization: Bearer your_private_token_here" http://laravel-blog.app/api/v1/posts
```

API are prefixed by ```api``` and the API version number like so ```v1```.

Do not forget to set the ```X-Requested-With``` header to ```XMLHttpRequest```. Otherwise, Laravel won't recognize the call as an AJAX request.

To list all the available routes for API :

```bash
$ docker-compose run --rm --no-deps blog-server php artisan route:list --path=api
```

## License

