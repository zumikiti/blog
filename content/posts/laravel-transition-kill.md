---
title: "プロセスの確認とキル方法"
date: 2023-02-28T15:47:04+09:00
---

laravel のプロジェクトで `DB::beginTransition();` して、確認を促しOK なら `DB::commit();` するスクリプトファイルがあった。  
そのスクリプトをサーバーで実行したところ、途中で ssh の接続が切れてしまって、再実行しても前回のプロセスが生きていて実行されないということがあった。その時の備忘録。

## プロセスの確認
```sh
$ ps agx | grep script_set_flag

24131 pts/3    S+     0:06 /usr/bin/real_php artisan laravel:script_set_flag
24146 pts/4    S+     0:00 grep --color=auto script_set_flag
```

上記の `24131` がスクリプトのプロセスIDです。 

## プロセスをキルする
```sh
kill 24131
```

問題なく実行できたら、もう一度 `grep` してみると消えている。

```sh
$ ps agx | grep script_set_flag

24156 pts/4    S+     0:00 grep --color=auto script_set_flag
```

## おまけ
そもそも ssh の接続が切れないようにする方法として `.ssh/config` で、 `ServerAliveInterval` を設定することで定期的にサーバに **no-op プロトコル** というもの送信してくれるようです。

以下のようにすると 60 秒毎に実行してくれます。

```sh
Host *hoge.com
  ServerAliveInterval 60
```

ただ、放っておいても ssh 接続が切れないので必ず作業後には切る癖つける必要があるので、ご注意を。
