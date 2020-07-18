---
title: "vim を起動しようとしたら、dyld: Library not loaded:とエラー"
date: 2020-07-18T22:15:34+09:00
tags: [vim, homebrew, perl]
---
`brew update && brew upgrade` したら、vim が起動しなくなった。

#### エラーメッセージ
```sh
$ vim .
dyld: Library not loaded: /usr/local/opt/perl/lib/perl5/5.28.1/darwin-thread-multi-2level/CORE/libperl.dylib
  Referenced from: /usr/local/bin/vim
  Reason: image not found
[1]    16413 abort      vim .
```

## 解決方法
https://superuser.com/questions/1341319/vim-fails-to-load-dynamic-library を参照しました。

```sh
$ brew install perl

Warning: perl 5.32.0 is already installed and up-to-date
To reinstall 5.32.0, run `brew reinstall perl`

$ brew reinstall perl
```
