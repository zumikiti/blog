---
title: "FATAL: Peer authentication failed for user と表示された"
date: 2020-06-11T11:58:31+09:00
tags: [postgres, psql]
---

## 事象
Homesated の postgres で既にあるユーザを再度 CREATE USER で作ってしまったら、以下エラーが出るようになった。

```sh
FATAL: Peer authentication failed for user "user_name"
```

## 解決手順
### 1. hba_file の場所を確認する
```sh
$ sudo su - postgres
$ psql

# show hba_file;
```

### 2. pg_hba.conf を sudo で開く
`show hba_file` で確認したファイルを `sudo` 権限で編集する。
```sh
sudo vim /etc/postgresql/11/main/pg_hba.conf
```
sudo で開かないと空の状態で表示されることに気づかず、時間を潰した・・

### 3. peer を md5 に修正する
```sh
# "local" is for Unix domain socketconnections only
local all all peer
```
を
```
local all all md5
```
と修正する

### 4. homestaed を再起動
`homestaed reload` で再起動したらエラーが解消しました。


## 参考
https://ryotatake.hatenablog.com/entry/2019/05/03/postgresql_peer_authentication_failed
