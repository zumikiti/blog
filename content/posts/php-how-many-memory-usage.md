---
title: "PHP の型ごとのメモリー使用量を調べてみた"
date: 2022-12-07T09:15:04+09:00
---

ふと、型によってメモリーをどれくらい使っているのか調べてみたくなった。

`memory_get_usage()` で使用しているメモリ数は確認できるようだった。

## 実行結果
大体予想通りだった。
- string > int
- boolean は一番使用メモリが少ない
- array は数に応じて増える
- 空の array は、空の string よりメモリー使用量は少ない

### int の場合
```php
Psy Shell v0.11.5 (PHP 8.1.8 — cli) by Justin Hileman
>>> $startMemory = memory_get_usage()
=> 24968640
>>> $hoge = 123
=> 123
>>> echo memory_get_usage() - $startMemory
108008⏎
```

### string の場合
```php
Psy Shell v0.11.5 (PHP 8.1.8 — cli) by Justin Hileman
>>> $startMemory = memory_get_usage()
=> 24968640
>>> $hoge = '123'
=> "123"
>>> echo memory_get_usage() - $startMemory
114664⏎

Psy Shell v0.11.5 (PHP 8.1.8 — cli) by Justin Hileman
>>> $startMemory = memory_get_usage()
=> 24968640
>>> $hoge = ''
=> ""
>>> echo memory_get_usage() - $startMemory
114600⏎
```

### boolean の場合
```php
Psy Shell v0.11.5 (PHP 8.1.8 — cli) by Justin Hileman
>>> $startMemory = memory_get_usage()
=> 24968640

>>> $hoge = true
=> true

>>> echo memory_get_usage() - $startMemory
95912⏎
```


### array の場合
```php
Psy Shell v0.11.5 (PHP 8.1.8 — cli) by Justin Hileman
>>> $startMemory = memory_get_usage()
=> 24968640
>>> $hoge = [1,2,3]
=> [
     1,
     2,
     3,
   ]
>>> echo memory_get_usage() - $startMemory
117576⏎

Psy Shell v0.11.5 (PHP 8.1.8 — cli) by Justin Hileman
>>> $startMemory = memory_get_usage()
=> 24968640
>>> $hoge = []
=> []
>>> echo memory_get_usage() - $startMemory
96336⏎
```

