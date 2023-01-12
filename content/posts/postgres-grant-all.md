---
title: "Postgres で全テーブルに権限を与える"
date: 2023-01-12T22:40:46+09:00
---

rootユーザでダンプファイルを流し込んだ後にプロジェクトのユーザーでテーブルを参照しようとしたら、`ERROR:  permission denied for database` が出た。

以下コマンドで、 public 配下の全テーブルに権限を付与することで解決できた。

```sql
GRANT ALL ON ALL TABLES IN SCHEMA public TO username;
```
