---
title: "Larastan の導入方法と実行サンプル"
date: 2022-12-25T21:12:40+09:00
---

Larastan は PHPStan の laravel 用のパッケージのようだ。

stan は static analyze の略で、文字通り静的解析ツールです。  
非常に簡単に導入できるし、結構細かく見てくれるようなので、実用性はありそうです。

簡単に導入方法を書きます。

## インストール
### 1. composer のインストール
```sh
composer require nunomaduro/larastan:^2.0 --dev
```

### 2. ディレクトリ直下に `phpstan.neon` か `phpstan.neon.dist` というファイル名で以下内容で作成します。
```sh
includes:
    - ./vendor/nunomaduro/larastan/extension.neon

parameters:

    paths:
        - app/

    # Level 9 is the highest level
    level: 5

#    ignoreErrors:
#        - '#PHPDoc tag @var#'
#
#    excludePaths:
#        - ./*/*/FileToBeExcluded.php
#
#    checkMissingIterableValueType: false
```

### 3. 実行
```sh
./vendor/bin/phpstan analyse
```

## 実行結果
試しにローカルのディレクトリで走らせてみた結果を貼る。

```sh
docker@f2c99798bceb:/var/www/html$ ./vendor/bin/phpstan analyse
Note: Using configuration file /var/www/html/phpstan.neon.
 27/27 [▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓] 100%

 ------ -----------------------------------------------------------------------------------
  Line   Console/Commands/ExportHLSVideo.php
 ------ -----------------------------------------------------------------------------------
  48     Method App\Console\Commands\ExportHLSVideo::handle() should return int but return
         statement is missing.
 ------ -----------------------------------------------------------------------------------

 ------ ------------------------------------------------------------------------------------
  Line   Http/Controllers/VideoController.php
 ------ ------------------------------------------------------------------------------------
  28     Method App\Http\Controllers\VideoController::store() should return
         Illuminate\Http\Response but returns Illuminate\Contracts\Routing\ResponseFactory.
  39     Method App\Http\Controllers\VideoController::show() should return
         Illuminate\Http\Response but return statement is missing.
  50     Method App\Http\Controllers\VideoController::edit() should return
         Illuminate\Http\Response but return statement is missing.
  62     Method App\Http\Controllers\VideoController::update() should return
         Illuminate\Http\Response but return statement is missing.
  73     Method App\Http\Controllers\VideoController::destroy() should return
         Illuminate\Http\Response but return statement is missing.
 ------ ------------------------------------------------------------------------------------


 [ERROR] Found 6 errors
```

## 参考
- [LarastanでLaravelプロジェクトを静的解析しよう！](https://qiita.com/MasaKu/items/7ed6636a57fae12231e0)
- [Larastan Github](https://github.com/nunomaduro/larastan)
