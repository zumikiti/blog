---
title: "Sqlite3 でテーブル一覧を表示する方法"
date: 2020-07-12T21:39:53+09:00
tags: ["sqlite3", ]
---

```sql
-- .tables でテーブル一覧表示
sqlite> .tables
stock_logs  user

sqlite> SELECT * FROM stock_logs ;
8411||132.8|5.77|10.30|0.39
```

以上

