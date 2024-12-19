---
title: "Kail Linux に PostgreSQL をインストール"
date: 2023-01-15T09:37:59+09:00
---

## 環境
- Kail Linux

## インストール
公式にしたがって、インストールする。

https://www.postgresql.org/download/linux/

```sh
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

sudo apt update

sudo apt -y install postgresql
```

## 起動
```sh
sudo service postgresql start
```

Active になっていれば、起動成功

```sh
$ sudo service postgres status

● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; preset: disabled)
     Active: active (exited) since Sun 2023-01-15 09:20:46 JST; 2s ago
    Process: 266999 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 266999 (code=exited, status=0/SUCCESS)
        CPU: 1ms

 1月 15 09:20:46 localhost systemd[1]: Starting PostgreSQL RDBMS...
 1月 15 09:20:46 localhost systemd[1]: Finished PostgreSQL RDBMS.
```

## PostgreSQL に入る
postgres ユーザーに切り替える

```sh
sudo su - postgres
```

PostgreSQL にログインする

```sh
psql
```

```sql
psql (15.0 (Debian 15.0-1)、サーバー 14.5 (Debian 14.5-2))
"help"でヘルプを表示します。

postgres=#
```
