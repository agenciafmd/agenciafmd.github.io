---
title: PHP 8.1
parent: Sail
nav_order: 2
---

# PHP 8.1

{: .no_toc }

## Conteúdo

{: .no_toc .text-delta }

1. TOC
   {:toc}

---

Este passo, deve ser utilizado somente para projetos existentes.

Novos projetos devem ser inicializados com o sail.

## Clonando o repositório

```bash
git clone git@gitlab.com:mixdinternet/prever-ampaz-site.git ~/code/ampaz
cd ~/code/ampaz
```

## Instalando o sail

Vamos instalar o sail no projeto usando a imagem do composer.

```bash 
docker run --rm \
    -u "$(id -u):$(id -g)" \
    -v "$(pwd):/var/www/html" \
    -w /var/www/html \
    laravelsail/php81-composer:latest \
    composer require laravel/sail --dev --ignore-platform-reqs
```

Agora, criamos o arquivo `docker-compose.yml` no projeto.

```yaml
services:
  ampaz.local:
    build:
      context: ./vendor/laravel/sail/runtimes/8.1
      dockerfile: Dockerfile
      args:
        WWWGROUP: '${WWWGROUP}'
    image: sail-8.1/app
    extra_hosts:
      - 'host.docker.internal:host-gateway'
    ports:
      - '${APP_PORT:-80}:80'
      - '${VITE_PORT:-5173}:${VITE_PORT:-5173}'
    environment:
      WWWUSER: '${WWWUSER}'
      LARAVEL_SAIL: 1
      XDEBUG_MODE: '${SAIL_XDEBUG_MODE:-off}'
      XDEBUG_CONFIG: '${SAIL_XDEBUG_CONFIG:-client_host=host.docker.internal}'
      IGNITION_LOCAL_SITES_PATH: '${PWD}'
    volumes:
      - '.:/var/www/html'
    networks:
      - sail
    depends_on:
      - mysql
      - mailpit
  mysql:
    image: 'mysql/mysql-server:8.0'
    ports:
      - '${FORWARD_DB_PORT:-3306}:3306'
    environment:
      MYSQL_ROOT_PASSWORD: '${DB_PASSWORD}'
      MYSQL_ROOT_HOST: '%'
      MYSQL_DATABASE: '${DB_DATABASE}'
      MYSQL_USER: '${DB_USERNAME}'
      MYSQL_PASSWORD: '${DB_PASSWORD}'
      MYSQL_ALLOW_EMPTY_PASSWORD: 1
    volumes:
      - 'sail-mysql:/var/lib/mysql'
      - './vendor/laravel/sail/database/mysql/create-testing-database.sh:/docker-entrypoint-initdb.d/10-create-testing-database.sh'
    networks:
      - sail
    healthcheck:
      test:
        - CMD
        - mysqladmin
        - ping
        - '-p${DB_PASSWORD}'
      retries: 3
      timeout: 5s
  mailpit:
    image: 'axllent/mailpit:latest'
    ports:
      - '${FORWARD_MAILPIT_PORT:-1025}:1025'
      - '${FORWARD_MAILPIT_DASHBOARD_PORT:-8025}:8025'
    networks:
      - sail
networks:
  sail:
    driver: bridge
volumes:
  sail-mysql:
    driver: local
```

{: .important-title }
> Importante
>
> Note a segunda linha do arquivo `docker-compose.yml` que define o nome do serviço como `ampaz.local`. Isso é
> importante para que o `sail` consiga identificar o serviço.

O nosso arquivo `.env` deve conter as seguintes variáveis:

```
APP_NAME=Ampaz
APP_ENV=local
APP_KEY=base64:KcliCTQ5RL9fwQ9utwhZIarVuy6JPHa6q9wL7suNplE=
APP_DEBUG=true
APP_URL=http://localhost:8080

DB_CONNECTION=mysql
DB_HOST=mysql
DB_USERNAME=sail
DB_PASSWORD=password
DB_DATABASE=sail

MAIL_DRIVER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025

FILESYSTEM_DRIVER=public

APP_SERVICE=ampaz.local
APP_PORT=8080
FORWARD_MAILPIT_PORT=1026
FORWARD_MAILPIT_DASHBOARD_PORT=8026
FORWARD_DB_PORT=33060
```

{: .important-title }
> Importante
>
> Note o último bloco de parâmetros do `.env`, aqui definimos as portas que serão utilizadas para cada serviço. É
> importante que essas portas sejam únicas entre os projetos.

Suba o ambiente com o comando:

```bash
sail up -d
```

Popule o banco com o comando:

```bash
sail artisan migrate:fresh --seed
```

Linke o storage com o comando:

```bash
sail artisan storage:link
```

Acessando o ambiente:

```bash
http://localhost:8080
```

{: .important-title }
> Importante
>
>Caso encontre problemas com o `redis`, faça um `sail composer update` e tente novamente.
