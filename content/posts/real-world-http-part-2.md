---
title: "Real World Http を読んだのでそのまとめ（第2章）"
date: 2020-02-17T15:24:20+09:00
tags: [http, go]
draft: true
---

# HTTP/1.0 のセマンティクス；ブラウザの基本機能の裏側

## 2.1 シンプルなフォームの送信（x-www-form-urlencoded）
フォームを使った POST 送信で一番シンプルな方法。
```html
<form method="POST">
    <input name="title">
    <input name="author">
    <input type="submit">
</form>
```

- `curl` コマンドでは、以下のようになります。
- `-d` で送る場合は、ブラウザ側は、`Content-Type: application/x-www-form-urlencoded` を設定します。これによって、区切りを `&` と `=` で繋いでいきます。 
```sh
$ curl --http1.0 -d title="hogehoge" -d author="hugahuga" http://localhost:1888

# server.go 側
title=hogehoge&author=hugahuga
```

- しかし、フォームの値の中に `&` や `=` が入ってくると区切りがわかりづらくなる。
- ブラウザは、RFC1866に定める変換フォーマットに従って変換を行う。
- `curl` では完全に再現することはできないが、類似するRFC3986で定義された方法で変換を行うと次のようになる。
- RFC1866 でも RFC3986 でも同じアルゴリズムで復元可能なため、さほど気にとめる必要はない。

```sh
$ curl --http1.0 --data-urlencode title="hoge & hoge" --data-urlencode author="hugahuga, hoge huga" http://localhost:1888

# server.go 側
title=hoge%20%26%20hoge&author=hugahuga%2C%20hoge%20huga
```

## 2.2 フォームを使ったファイルの送信（multipart/form-data）
- HTMLのフォームでは、オプションでマルチポートフォーム形式というエンコードタイプが選択できる。
- RFC1867 で定義されています。

```html
<form action="POST" enctype="multipart/form-data">
</form>
```

- `curl` では `-F` をつけることで、`multipart/form-data` 形式で送信することができます。
- `-d` と `-F` を同時に使用することはできません。
```sh
$ curl --http1.0 -F title="hoge & hoge" -F author="fuga fuga" -F attachment-file=@server.go http://localhost:1888

# server.go 側
POST / HTTP/1.0
Host: localhost:1888
Connection: close
Accept: */*
Content-Length: 954
Content-Type: multipart/form-data; boundary=------------------------17e49a7786658cb5
User-Agent: curl/7.54.0

--------------------------17e49a7786658cb5
Content-Disposition: form-data; name="title"

hoge & hoge
--------------------------17e49a7786658cb5
Content-Disposition: form-data; name="author"

fuga fuga
--------------------------17e49a7786658cb5
Content-Disposition: form-data; name="attachment-file"; filename="server.go"
Content-Type: application/octet-stream

package main

import (
  ...
)

func main() {
  ...
}

--------------------------17e49a7786658cb5--
```

- 送信するファイル名やファイル形式を手動で設定することもできます

```sh
# 送信ファイル名とローカルファイル名は同じ。形式も自動設定。
$ curl --http1.0 -F attachment-file=@server.go

# 形式は手動設定。
$ curl --http1.0 -F "attachment-file=@server.go;text/html"

# ファイル名は指定したファイル名を利用。
$ curl --http1.0 -F "attachment-file=@server.go;filename=sample.go"
```

## 2.3 フォームを利用したリダイレクト
- 一章で紹介した300番台のステータスコードを使ったリダイレクトにはいくつか制限がある
  - URL には 2000 文字に収めるべき、という目安があるので、GET のクエリーで送信できるデータ量に制限がある。
  - データが URL に入ってしまうため、送信したい内容がアクセスログに残ってしまう懸念がある。
- それを回避する方法としてたまに利用されるのが、HTML フォームを利用したリダイレクト。
  - リダイレクト先に送りたいデータが `<input type="hidden">` タグに HTML が帰ってきます。フォームの送信先がリダイレクト先です。
  - メリットは、IE でもデータ量の制限がない点。
  - デメリットは、一瞬白紙のページが表示されてしむ点と、JavaScriput が無効にされていると自動で遷移しない点。
```html
<!DOCTYPE html>
<html>
<body onload="document.forms[0].submit()">
  <form action="リダイレクトしたい先" method="post">
    <input type="hidden" name="data" value="送りたいメッセージ">
    <input type="submit" value="Continue">
  </form>
</body>
</html>
```

## 2.4 コンテントネゴシエーション
クライアントとサーバーは別々に開発されているため、両者が期待している形式や設定が一致しているとは限らない。通信方式を最適化するために、1リクエストの中でサーバーとクライアントがお互いベストな設定を共有する仕組みがコンテントネゴシエーション。

### 2.4.1 ファイルの種類の決定
```
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
```

### 2.4.2 表示言語の決定
```
Accept-Language: ja,en-US;q=0.7,en;q=0.3
```

### 2.4.3 キャラクターセットの決定
```
content-type: text/html; charset=UTF-8
```

### 2.4.4 圧縮による通信速度の向上 
```
Accept-Encoding: gzip, deflate, br
```

```sh
$ curl --http1.0 --compressed http://localhost:1888

# server.go 側
GET / HTTP/1.0
Host: localhost:1888
Connection: close
Accept: */*
Accept-Encoding: deflate, gzip
User-Agent: curl/7.54.0
```
