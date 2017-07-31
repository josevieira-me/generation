# Generation

Keeping it uniform between development, staging and production environments is often something not easy.
On the last years, our buddy Docker has become more and more mature and now it's becoming the standard.

No more "it worked on my machine"!

## What is does?
Generation can help you doing some amazing things, the 3 main scenarios are listed above:

- **Run Laravel and/or Vue.JS in Development.**
- **Run Laravel and/or Vue.JS in Production.**
- **Replace local dependencies with Docker commands.**

## What do I need to know before getting started?
Before staging with Generation, a few pieces of knowledge must be in place:

#### For running a development Laravel or Vue.JS environment:
- Know how to operate `docker-compose`.

## Notice
Docker compose shipped with Docker is usually very old.
Please have the latest version installed from Github at https://github.com/docker/compose/releases.

## Images
If you are already comfortable with the tools and have played around Generation, here are the set of images available for usage,
so you can start building your environment with the tools that you may want.

|Repository                 | Images/Tags                   | Description                                        |
|---------------------------|-------------------------------|----------------------------------------------------|
| generation/**php**        | `7.1`, `latest`               | PHP v7.1 for command line and queues               |
|                           | `7.1-nginx`, `latest-nginx`   | PHP v7.1 with Nginx webserver                      |
|                           | `7.1-caddy`, `latest-caddy`   | PHP v7.1 with Caddy webserver                      |
|                           | `7.0`,                        | PHP v7.0 for command line and queues               |
|                           | `7.0-nginx`,                  | PHP v7.0 with Nginx webserver                      |
|                           | `7.0-caddy`,                  | PHP v7.0 with Caddy webserver                      |
| generation/**node**       | `8`, `latest`                 | Node.js v8.x                                       |
|                           | `7`                           | Node.js v7.x                                       |
|                           | `6`                           | Node.js v6.x                                       |
| generation/**mysql**      | `8`, `latest`                 | MySQL Server v8 (with sql-mode='')                 |
|                           | `5.7`                         | MySQL Server v5.7                                  |
| generation/**postgres**   | `10`, `latest`                | PostgreSQL Server v10                              |
|                           | `9.6`                         | PostgreSQL Server v9.6                             |
|                           | `9.5`                         | PostgreSQL Server v9.5                             |
|                           | `9.4`                         | PostgreSQL Server v9.4                             |
|                           | `9.3`                         | PostgreSQL Server v9.3                             |
| generation/**redis**      | `4.0`, `latest`               | Redis Server v4.0                                  |
|                           | `3.2`                         | Redis Server v3.2                                  |
| generation/**mailcatcher**| `latest`                      | MailCatche is a hosted alternative to MailTrap.io  |



### I do have a project, and I want to run it using docker
Well, that's the whole point of the project, the commands there was designed for quick usage of stand alone commands, so we have a great alternative when we have a project already, we can define a docker-compose.yml file that will expose and run the services we need.

> **Understanding the docker-compose compose tool is appreciated in order to use the following configuration files.**

#### Laravel docker-compose.yml


```yml
####
# ATENTION:
# Replace all occurences of sandbox with your project's name
####

# v2 syntax
version: '2'

# Named volumes
volumes:
  # Postgres Data
  sandbox-postgres-data:
    driver: local

  # MySQL Data
  sandbox-mysql-data:
    driver: local

  # Redis Data
  sandbox-redis-data:
    driver: local

services:
  # Postgres (9.6)
  postgres:
    image: generation/postgres:9.6
    container_name: sandbox-postgres
    volumes:
      - sandbox-postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_PASSWORD=sandbox
      - POSTGRES_DB=sandbox
      - POSTGRES_USER=sandbox

  # MySQL (5.7)
  mysql:
    image: generation/mysql:5.7
    container_name: sandbox-mysql
    volumes:
      - sandbox-mysql-data:/var/lib/mysql
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=sandbox
      - MYSQL_DATABASE=sandbox
      - MYSQL_USER=sandbox
      - MYSQL_PASSWORD=sandbox

  # Redis
  cache:
    image: generation/redis:4.0
    container_name: sandbox-redis
    command: --appendonly yes
    volumes:
      - sandbox-redis-data:/data
    ports:
      - "6379:6379"

  # PHP (with Caddy)
  app:
    image: generation/php:7.1-caddy
    container_name: sandbox-app
    volumes:
      - .:/var/www/app
    ports:
      - "80:8080"
    links:
      - postgres
      - mysql
      - cache

  # Laravel Queues
  queue:
    image: generation/php:7.1
    container_name: sandbox-queue
    command: php artisan queue:listen
    volumes:
      - .:/var/www/app
    links:
      - mysql
      - cache
```

### Vue.js docker-compose.yml
Developing with Vue.js? We got you covered! Here is the docker-compose file:

```yml
version: '2'

services:
  # Web server - For live reload and development
  # This environment can be used to run npm and node
  # commands as well
  dev:
    image: generation/node:6
    container_name: sandbox-vue-dev
    command: npm run dev
    volumes:
      - .:/var/www/app
    ports:
      - 8080:8080

  # Testing dist on a "real" webserver
  production-server:
    image: nginx:stable-alpine
    container_name: sandbox-preview-server
    volumes:
      - ./dist:/usr/share/nginx/html
    ports:
      - 9090:80
```
