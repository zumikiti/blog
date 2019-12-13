---
title: "Homebrew で php7.4 にアップデートする"
date: 2019-12-13T11:58:45+09:00
tags: [php, homebrew, php74]
---

## php のバージョン確認
```sh
$ php -v
PHP 7.2.24 (cli) (built: Oct 25 2019 11:14:44) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.2.24, Copyright (c) 1999-2018, by Zend Technologies
```

## Homebrew で php7.4 を探す
```sh
$ brew search php@7.4
==> Formulae
php@7.4
```

## php7.4 をリンクさせる
```sh
brew link php@7.4
Linking /usr/local/Cellar/php/7.4.0...
Error: Could not symlink bin/pear
Target /usr/local/bin/pear
is a symlink belonging to php@7.2. You can unlink it:
  brew unlink php@7.2

To force the link and overwrite all conflicting files:
  brew link --overwrite php

To list all files that would be deleted:
  brew link --overwrite --dry-run php
```

php7.2 を先にアンリンクする必要があった。
```sh
$ brew unlink php@7.2
Unlinking /usr/local/Cellar/php@7.2/7.2.24... 25 symlinks removed
```

再度リンクさせる
```sh
$ brew link php@7.4
Linking /usr/local/Cellar/php/7.4.0... 24 symlinks created

# php のバージョンが php7.4 になっているのを確認できたらOK。
$ php -v
PHP 7.4.0 (cli) (built: Nov 29 2019 16:18:30) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.0, Copyright (c), by Zend Technologies
```
