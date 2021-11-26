---
title: "複数のプロジェクトで Docker を使用する場合の個人的ベストプラクティス"
date: 2021-11-26T14:01:02+09:00
---

社内でDockerを利用したプロジェクトが増えてきて、ポートの競合問題やコンテナが多すぎて重いなどの問題があり、ひとまず個人的なペストプラクティスを書く。

## 概要
- DB や mailhog などの共通で利用できるコンテナは共通化する
- `nginx-proxy` でリバースプロキシして web のポート競合も回避する
- `laravel` のプロジェクトでしか私は使用していないが、汎用性はあると思う

## ディレクトリ構成
```
.
├── webdev
│   ├── dump/
│   └── docker-compose.yaml
├── project1
│   ├── docker-compose.yaml
│   └── etc...
└── project2
    ├── docker-compose.yaml
    └── etc...
```

## 共通コンテナの設定
adwin さんが公開してくれているリポジトリに `nginx-proxy` を追加いただけ。
https://github.com/adwinying/webdev

### docker-compose.yaml
- 共通化することで、ポート競合を回避する
- `docker-compose -d pgsql mailhog proxy` と必要なコンテナのみ起動する
- ダンプファイルなどがあれば、`dump` ディレクトリを作成して、そこにダンプファイルを格納する

```yaml
version: '3'
services:
  mysql:
    image: mysql:5.7
    ports:
      - '${MYSQL_PORT:-3306}:3306'
    environment:
      MYSQL_ROOT_PASSWORD: '${DB_PASSWORD:-password}'
      MYSQL_DATABASE: '${DB_DATABASE:-db}'
      MYSQL_USER: '${DB_USERNAME:-sail}'
      MYSQL_PASSWORD: '${DB_PASSWORD:-password}'
      MYSQL_ALLOW_EMPTY_PASSWORD: 'yes'
    volumes:
      - 'webdev-mysql:/var/lib/mysql'
      - './dump:/tmp/dump'
    networks:
      - webdev
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-p${DB_PASSWORD:-password}"]
      retries: 3
      timeout: 5s


  pgsql:
    image: postgres:13
    ports:
      - '${PGSQL_PORT:-5432}:5432'
    environment:
      PGPASSWORD: '${DB_PASSWORD:-password}'
      POSTGRES_DB: '${DB_DATABASE:-db}'
      POSTGRES_USER: '${DB_USERNAME:-sail}'
      POSTGRES_PASSWORD: '${DB_PASSWORD:-password}'
    volumes:
      - 'webdev-pgsql:/var/lib/postgresql/data'
      - './dump:/tmp/dump'
    networks:
      - webdev
    healthcheck:
      test: ["CMD", "pg_isready", "-q", "-d", "${DB_DATABASE:-db}", "-U", "${DB_USERNAME:-sail}"]
      retries: 3
      timeout: 5s


  redis:
    image: 'redis:alpine'
    ports:
      - '${REDIS_PORT:-6379}:6379'
    volumes:
      - 'webdev-redis:/data'
    networks:
      - webdev
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      retries: 3
      timeout: 5s


  meilisearch:
    image: 'getmeili/meilisearch:latest'
    ports:
      - '${MEILISEARCH_PORT:-7700}:7700'
    volumes:
      - 'webdev-meilisearch:/data.ms'
    networks:
      - webdev


  mailhog:
    image: 'mailhog/mailhog:latest'
    ports:
      - '${MAILHOG_PORT:-1025}:1025'
      - '${MAILHOG_DASHBOARD_PORT:-8025}:8025'
    networks:
      - webdev


  proxy:
    image: jwilder/nginx-proxy
    container_name: nginx-proxy
    ports:
      - "127.0.0.1:80:80"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
    restart: always
    privileged: true
    networks:
      - webdev


networks:
  webdev:
    name: webdev
    driver: bridge


volumes:
  webdev-mysql:
    driver: local
  webdev-pgsql:
    driver: local
  webdev-redis:
    driver: local
  webdev-meilisearch:
    driver: local
```

## プロジェクト毎の設定
共通コンテナを立ち上げた後、プロジェクト毎で立ち上げる必要があるのは、web のコンテナだけになる。

### docker-compose.yaml
- ports は 80 だけ指定すれば、コンテナ側のポートは適当に割り当ててくれる
- environment に VIRTUAL_HOST を設定することで、`http://laravel.test` などで、nginx-proxy がよしなにしてくれる
- 共通コンテナと同一ネットワークに所属する必要があるので、 `networks` に `webdev` を指定している

```yaml
version: '3'

services:
  app:
    image: thecodingmachine/php:7.4-v4-apache
    ports:
      - '80'
    environment:
      PHP_EXTENSIONS: bcmath gd intl pdo_pgsql pgsql
      PHP_INI_MEMORY_LIMIT: 512M
      PHP_INI_DATE__TIMEZONE: Asia/Tokyo
      PHP_INI_MBSTRING__LANGUAGE: Japanese
      APACHE_DOCUMENT_ROOT: /var/www/html/public
      VIRTUAL_HOST: 'laravel.test'
    volumes:
      - '.:/var/www/html'
    networks:
      - webdev

networks:
  webdev:
    external: true
    name: webdev
```

## おまけ
少しズレるが、`docker-compose` コマンドが長すぎるので、Taskfile の設定も置いておきます。  

**使用手順**
1. `docker-compose.yaml` と同一階層に置く
2. `chmod 755 ./Taskfile`
3. `alias task=./Taskfile` と設定する
これで準備完了です。

`task artisan migrate` などで実行できるので便利！

### Taskfile
```sh
#!/bin/bash
# https://github.com/adriancooney/Taskfile#install

function up {
  docker-compose up -d
}

function down {
  docker-compose down
}

function restart {
  docker-compose restart
}

function exec {
  docker-compose exec app "$@"
}

function bash {
  exec bash
}

function php {
  exec /usr/bin/php "$@"
}

function artisan {
  exec /usr/bin/php artisan "$@"
}

function composer {
  exec /usr/local/bin/composer "$@"
}

function test {
  exec vendor/bin/phpunit "$@"
}

function tinker {
  artisan tinker
}

function start {
  echo "start task not implemented"
}

function default {
  help
}

function help {
    echo "$0 <task> <args>"
    echo "Tasks:"
    compgen -A function | cat -n
}

TIMEFORMAT="Task completed in %3lR"
time ${@:-default}
```
