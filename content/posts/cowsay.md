---
title: "cowsay で遊ぶ"
date: 2019-12-13T12:32:27+09:00
tags: [php, cowsay, kriby]
---

cowsay というプロジェクトをご存知だろうか。
その名の通り、牛に言葉を喋らせるという高尚なプロジェクトである。
私も兼ねてから、このプロジェクトに参加したいと思っていました。

そして今日、牛に言葉を話させることに成功した。

```sh
$ php say.php

< Ohmg I'm a cow! >
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

さらに、タックスにも言葉を喋らせられる。
```sh
$ php say.php -ctux -m僕はタックス！

< 僕はタックス！ >
   \
    \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
```

さらには、ドラゴンも召喚できる。
```sh
$ php say.php -cdragon -mお腹がすいたよ

< お腹がすいたよ >
      \                    / \  //\
       \    |\___/|      /   \//  \\
            /0  0  \__  /    //  | \ \
           /     /  \/_/    //   |  \  \
           @_^_@'/   \/_   //    |   \   \
           //_^_/     \/_ //     |    \    \
        ( //) |        \///      |     \     \
      ( / /) _|_ /   )  //       |      \     _\
    ( // /) '/,_ _ _/  ( ; -.    |    _ _\.-~        .-~~~^-.
  (( / / )) ,-{        _      `-.|.-~-.           .~         `.
 (( // / ))  '/\      /                 ~-. _ .-~      .-~^-.  \
 (( /// ))      `.   {            }                   /      \  \
  (( / ))     .----~-.\        \-'                 .~         \  `. \^-.
             ///.----..>        \             _ -~             `.  ^-`  ^-_
               ///-._ _ _ _ _ _ _}^ - - - - ~                     ~-- ,.-~
                                                                  /.-~
```

素晴らしい。

我が物のように書いてきたが、これは公開されているリポジトリを使っているだけです。
https://github.com/alrik11es/cowsayphp

## コマンドラインで牛に言葉を喋らせる
### リポジトリをクローンする。
ただ喋らすだけなら、クローンせず `composer require alrik11es/cowsayphp` で良いのだが、後ほどキャラクターを追加したいので、クローンする。
```sh
$ git clone git@github.com:zumikiti/cowsayphp.git
$ cd cowsayphp/
```

### say.php を作る
```php:say.php
<?php
require 'vendor/autoload.php';

use Cowsayphp\Farm;
use Cowsayphp\Farm\Cow;
use Cowsayphp\Farm\Dragon;
use Cowsayphp\Farm\Tux;
use Cowsayphp\Farm\Whale;

// コマンドラインから -c と -m を受け取る
$args = getopt('c:m:');

// それぞれ変数に代入
$char = $args['c'] ?? 'cow';
$msg  = $args['m'] ?? 'Ohmg I\'m a cow!';

// cowsayphp にデフォルトで用意されているキャラクターを代入
switch ($char) {
    case 'tux':
        $cow = Farm::create(Tux::class);
        break;
    case 'dragon':
        $cow = Farm::create(Dragon::class);
        break;
    case 'whale':
        $cow = Farm::create(Whale::class);
        break;
    default:
        $cow = Farm::create(Cow::class);
}

// 喋らす
echo $cow->say($msg);
```

### コマンドラインで実行する
```sh
$ php say.php
< Ohmg I'm a cow! >
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

引数に `-c` にキャラクター名、`-m` で喋らせたい言葉を指定できます。
```sh
$ php say.php -ctux -m僕はタックス

< 僕はタックス >
   \
    \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
```

## キャラクターを追加する
自分の好きなキャラクターを追加したいですよね。

例えば、みんな大好きカービィを追加したい。
`src/Farm/Kirby.php` を追加する
なお、カービィのAAは https://smashwiki.info/%E3%82%AB%E3%83%BC%E3%83%93%E3%82%A3%E3%81%AEAA からお借りしました。
```php:Kirby.php
<?php
namespace Cowsayphp\Farm;

use Cowsayphp\AbstractAnimal;

class Kirby extends AbstractAnimal
{
    protected $character = <<<DOC

{{bubble}}
        \   ,-‐――､
         \/   ┃ ┃  ヽ-､
          し  " ∇ " |‐'
          ヽ___  ＿ノ、
          'ｰ-'￣ `ｰ-'   

DOC;
}
```

say.php の switch に kirby を追加
```php:say.php
    case 'kirby':
        $cow = Farm::create(Kirby::class);
        break;
```

コマンドラインで実行する
```sh
php say.php -ckirby

< Ohmg I'm a cow! >
        \   ,-‐――､
         \/   ┃ ┃ ヽ -､
          し  " ∇ " |‐'
          ヽ___  ＿ノ、
          'ｰ-'￣ `ｰ-'
```

### カービィのデフォルトのメッセージは`ぽよぽよ〜`にしたい。

```diff:say.php
- $msg = $args['m'] ?? "Ohmg I'm a cow!";

switch ($char) {
...

    case 'kirby':
        $cow = Farm::create(Kirby::class);
+         $msg = 'ぽよぽよ〜';
        break;

...

}

- echo $cow->say($msg);
+ echo $cow->say($msg ?? ($args['m'] ?? 'Ohmg I\'m a cow!'));
```

コマンドラインで実行する
```sh
php say.php -ckirby

< ぽよぽよ〜 >
        \   ,-‐――､
         \/   ┃ ┃  ヽ-､
          し  " ∇ " |‐'
          ヽ___  ＿ノ、
          'ｰ-'￣ `ｰ-'
```

## 終わりに
私が普段 PHP を使っているので、一番いじりやすいと感じた `cowsayphp` を使ってみましたが、cowsay は多くの言語で実装されており、さらには cow 以外のキャラクターを使ったものも多く存在します。

ざっと、GitHub で検索しただけでも以下リポジトリが見つかりました。

- Neo-cowsay https://github.com/Code-Hex/Neo-cowsay
- cowsay https://github.com/piuccio/cowsay
- ponysay https://github.com/erkin/ponysay
- pokemonsay https://github.com/possatti/pokemonsay
- parrotsay https://github.com/matheuss/parrotsay

今回は、公開リポジトリを触ってみるだけになってしまいましたが、来年は私もコマンドラインで遊べる何かを作って公開してみたいと思いました。
