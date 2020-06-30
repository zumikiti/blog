---
title: "Homestaed(ubuntu + nginx) で、php.ini の設定を変更する"
date: 2020-06-30T15:42:11+09:00
---

## php.ini の場所を確認する
```sh
$ php --ini
```

php のバージョンによって、異なる。

## php.ini は cli と fpm で異なる
- cli: コマンドライン
- fpm: FPM ( FastCGI Process Manager ) は PHP の FastCGI 実装のひとつ [PHP: FastCGI Process Manager (FPM) - Manual](https://www.php.net/manual/ja/install.fpm.php)

### ngin と php-fpm
homestaed だと nginx + php-fpm がデフォルトで設定されている。
homestaed.yaml で設定した server 情報は、`/etc/nginx/sites-enabled/` 配下にある。

```config
# vim /etc/nginx/sites-enabled/hoge.test
server {
  listen 80;
  listen 443 ssl http2;
  server_name .hoge.test;
  root "/home/vagrant/hoge/public";

  index index.html index.htm index.php;

  charset utf-8;



  location / {
    try_files $uri $uri/ /index.php?$query_string;

  }



  location = /favicon.ico { access_log off; log_not_found off; }
  location = /robots.txt  { access_log off; log_not_found off; }

  access_log off;
  error_log  /var/log/nginx/hoge.test-error.log error;

  sendfile off;

  location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;


    fastcgi_intercept_errors off;
    fastcgi_buffer_size 16k;
    fastcgi_buffers 4 16k;
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
  }

  location ~ /\.ht {
    deny all;
  }

  ssl_certificate     /etc/nginx/ssl/hoge.test.crt;
  ssl_certificate_key /etc/nginx/ssl/hoge.test.key;
}
```

`php7.4-fpm` を使っていることがわかる。

## php.ini を書き換えて反映させる
上記で説明した通り、php.ini は `cli` と `fpm` で分かれている

### cli の php.ini を修正反映させる
1. `php --ini` で表示された PHP のバージョンの `/cli/php.ini` を変更する
2. `server nginx restart` を実行する。（vagrant の初期 PW は `vagrant`）

以上

### fpm の php.ini を修正反映させる
1. `vim /etc/nginx/sites-enabled/` で配下の変更したいサーバのファイルを開く
2. `fastcgi_pass unix:/var/run/php/phpX.X-fpm.sock;` で該当の php-fpm を確認する
3. 2 で確認した PHP バージョンの `fpm/php.ini` を修正する。
4. `server nginx restart` と `server phpX.X-fpm restart` を実行する。

以上

なお、サイト上で反映しているかは、

```php
// post_max_size を確認したい場合の例
dd(ini_get('post_max_size')); 
```
で確認できる

## 参考
- https://qiita.com/kotarella1110/items/634f6fafeb33ae0f51dc
- https://www.spiceworks.co.jp/blog/?p=12317
- https://blog.e2info.co.jp/2016/11/02/lets-php-fpm-restart/
- https://teratail.com/questions/117587

