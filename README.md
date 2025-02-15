## docker-lemp Optimized for Inquid stack

> If you want to use PHP7.4 on LEMP stack then head over to
[`adhocore/lemp:7.4`](https://github.com/adhocore/docker-lemp/tree/7.4).

It is quick jumpstart for onboarding you into docker based development.

The docker container `adhocore/lemp` is composed of:

Name          | Version    | Port
--------------|------------|------
adminer       | 4.7.7      | 80
alpine        | 3.12       | -
MySQL`*`      | 5.7        | 3306
nginx         | 1.18.0     | 80
phalcon       | 4.0.0      | -
PHP           | 8.0.3      | 9000
redis         | 5.0.9      | 6379
swoole        | 4.4.12     | -

> `*`: It is actually MariaDB 10.4.13.

## Usage

Install [docker](https://docs.docker.com/install/) in your machine.
Also recommended to install [docker-compose](https://docs.docker.com/compose/install/).

```sh
# pull latest image
docker pull adhocore/lemp:8.0

# Go to your project root then run
docker run -p 8080:80 -p 8888:88 -v `pwd`:/var/www/html --name lemp -d adhocore/lemp:8.0

# In windows, you would use %cd% instead of `pwd`
docker run -p 8080:80 -p 8888:88 -v %cd%:/var/www/html --name lemp -d adhocore/lemp:8.0

# If you want to setup MySQL credentials, pass env vars
docker run -p 8080:80 -p 8888:88 -v `pwd`:/var/www/html \
  -e MYSQL_ROOT_PASSWORD=1234567890 -e MYSQL_DATABASE=appdb \
  -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=123456 \
  --name lemp -d adhocore/lemp:8.0
```

After running container as above, you will be able to browse [localhost:8080](http://localhost:8080)!

The database adminer will be available for [mysql](http://localhost:8080/adminer?server=127.0.0.1%3A3306&username=root).

### Stop container

To stop the container, you would run:

```sh
docker stop lemp
```

### (Re)Start container

You dont have to always do `docker run` as in above unless you removed or lost your `lemp` container.

Instead, you can just start when needed:

```sh
docker start lemp
```

> **PRO** If you develop multiple apps, you can create multiple lemp containers with different names.
>
> eg: `docker run -p 8081:80 -v $(pwd):/var/www/html --name new-lemp -d adhocore/lemp:8.0`


## With Docker compose

Create a `docker-compose.yml` in your project root with contents something similar to:

```yaml
# ./docker-compose.yml
version: '3'

services:
  app:
    image: adhocore/lemp:8.0
    # For different app you can use different names. (eg: )
    container_name: some-app
    volumes:
      # app source code
      - ./path/to/your/app:/var/www/html
      # db data persistence
      - db_data:/var/lib/mysql
      # Here you can also volume php ini settings
      # - /path/to/zz-overrides:/usr/local/etc/php/conf.d/zz-overrides.ini
    ports:
      - 8080:80
    environment:
      MYSQL_ROOT_PASSWORD: supersecurepwd
      MYSQL_DATABASE: appdb
      MYSQL_USER: dbusr
      MYSQL_PASSWORD: securepwd

volumes:
  db_data: {}
```

Then all you gotta do is:

```sh
# To start
docker-compose up -d

# To stop
docker-compose stop
```

As you can see using compose is very neat, intuitive and easy.
Plus you can already set the volumes and ports there, so you dont have to type in terminal.

### MySQL Default credentials

- **root password**: 1234567890 (if `MYSQL_ROOT_PASSWORD` is not passed)
- **user password**: 123456 (if `MYSQL_USER` is passed but `MYSQL_PASSWORD` is not)

#### Accessing DB

In PHP app you can access MySQL db via PDO like so:
```php
$db = new PDO(
    'mysql:host=127.0.0.1;port=3306;dbname=' . getenv('MYSQL_DATABASE'),
    getenv('MYSQL_USER'),
    getenv('MYSQL_PASSWORD')
);
```

### Nginx

URL rewrite is already enabled for you.

Either your app has `public/` folder or not, the rewrite adapts automatically.

### PHP

For available extensions, check [adhocore/phpfpm#extension](https://github.com/adhocore/docker-phpfpm/tree/8.0b#extensions).

### USE IN PRODUCTION
> Do not use this LEMP in Production.
> For production, use [adhocore/phpfpm](https://github.com/adhocore/docker-phpfpm)
> then [compose](https://docs.docker.com/compose/install/) a stack using individual `nginx`, `redis`, `mysql` etc images.

[`adhocore/lemp`](https://hub.docker.com/r/adhocore/lemp) is a minimal single container LEMP full stack for local development.
