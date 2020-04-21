---
title: "Laravel Config Cache Error"
date: 2020-04-12T19:42:29+09:00
---

```sh
UnexpectedValueException
There is no existing directory at "/Users/hoge/projects/xxx/storage/logs" and its not buildable: Permission denied
```

というエラーが `config:cache` を行ったら出た。

## 対処
以下を実行したら、改善した。

```sh
php artisan cache:clear
php artisan route:clear
php artisan config:clear
```
