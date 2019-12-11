---
title: "Real World Httpを読んだので、まとめ（第1章）"
date: 2019-12-10T06:31:54+09:00
draft: true
---

## 1章
- http 0.9 は1990 年に CERN によって考案された。
- http 0.9 はとてもシンプルで、テキストを受け取るだけ。また、受け取れるのは、/greeting というパスの部分のみ
- go で簡易的な HTTP サーバーを作り、curl でレスポンスをみてみる。

```go
# server.go
package main

import (
	"fmt"
	"log"
	"net/http"
	"net/http/httputil"
)

func handler(w http.ResponseWriter, f *http.Request) {
	dump, err := httputil.DumpRequest(f, true)
	if err != nil {
		http.Error(w, fmt.Sprint(err), http.StatusInternalServerError)
		return
	}
	fmt.Println(string(dump))
	fmt.Fprint(w, "<html><body>hello</body></html>\n")
}

func main() {
	var httpServer http.Server
	http.HandleFunc("/", handler)
	log.Println("start http listing :1888")
	httpServer.Addr = ":1888"
	log.Println(httpServer.ListenAndServe())
}
```

サーバーを起動する。

```sh
$ go run server.go
2019/12/07 08:07:55 start http listing :1888
```

`curl` コマンドでサーバーにリクエストを送ってみる

```sh
# 単純な GET 
$ curl --http1.0 http://localhost:1888/greeting
<html><body>hello</body></html> 
```

```sh
# 検索条件追加した場合
# --data-urlencode は --get があるときに動作
# http://localhost:1888?search=world となる"
$ curl --http1.0 --get --data-urlencode "search world" http://localhost:1888

[server.go 側]
GET /?search%20world HTTP/1.0
Host: localhost:1888
Connection: close
Accept: */*
User-Agent: curl/7.54.0
```

```sh
# 詳細なレスポンスの内容をみる（-v）
$ curl -v --http1.0 http://localhost:1888
* Rebuilt URL to: http://localhost:1888/
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 1888 (#0)
> GET / HTTP/1.0
> Host: localhost:1888
> User-Agent: curl/7.54.0
> Accept: */*
>
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Date: Fri, 06 Dec 2019 22:57:28 GMT
< Content-Length: 32
< Content-Type: text/html; charset=utf-8
<
<html><body>hello</body></html>
* Closing connection 0
```

## 1.4 電子メール
### 1.4.1 ヘッダーの送信
mail ヘッダーの送信（-H）
```sh
$ curl --http1.0 -H "X-Text: Hello" http://localhost:1888

[server.go 側]
2019/12/07 08:07:55 start http listing :1888
GET / HTTP/1.0
Host: localhost:1888
Connection: close
Accept: */*
User-Agent: curl/7.54.0
X-Text: Hello
```

User-Agent を偽装する
```sh
$ curl --http1.0 -A "Mozille/5.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/6.0)" http://localhost:1888

[server.go 側]
GET / HTTP/1.0
Host: localhost:1888
Connection: close
Accept: */*
User-Agent: Mozille/5.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/6.0)
```

### 1.4.3 MIME タイプ
ファイルの種類を区分するための文字列で、電子メールのために作られた。初出は1992年のRFC1341です。
ブラウザでは、MIME タイプによって画面に表示したり、保存ダイアログを出したりといった機能を提供します。

### 1.4.5 電子メールとの違い
- ヘッダー、本文はほぼ同じ
- HTTPのリクエストでは、先頭にメソッドとパスの行が追加される
- HTTPのレスポンスでは、先頭にステータスコードが追加される

HTTP の通信は高速で電子メールが往復しているとも言える。

HTTP もヘッダーを改行コードで折り返すことを許可していたが、一部ブラウザでうまく動作せず、レスポンス分割攻撃という攻撃に悪用されることもあったため、RFC7230 で非推奨となりました。

## 1.5 HTTP の先祖ニュースグループ
ニュースグループという大規模掲示板があった。
複数のサーバがマスター・スレーブ構造で繋がっており、スレーブのサーバーがマスターのサーバーに定期的に情報を取りに行く。この通信に使用されていたのが、Network News Transfer Protocol (NNTP)。
HTTP/0.9 の 5年前の1986年に RFC977 で定義されています。

```sh
# POST リクエスト
$ curl --http1.0 -X POST http://localhost:1888/greeting
```

```sh
# server.go側
POST /greeting HTTP/1.0
Host: localhost:1888
Connection: close
Accept: */*
User-Agent: curl/7.54.0
```

### 1.5.2 ステータスコード
- 100 番台: 処理中の情報伝達。特殊な用途でのみ使用
- 200 番台: 成功時のレスポンス。ステータスコード 200 が一般的。
- 300 番台: サーバーからクライアントへの命令。リダイレクトやキャッシュ利用時など。
- 400 番台: クライアントからのリクエストがおかしい時。
- 500 番台: サーバ内部でのエラー。

## 1.6 リダイレクト
300 番台のステータスの一部は、サーバーがブラウザに対して、指示するステータスコードです。300 以外の場合は、Location ヘッダーを使ってリダイレクト先をサーバーからクライアントへ伝達します。

| ステータスコード | メソッドの変更 | 恒久的 / 一時的 | キャッシュ | 説明 |
|--

- 恒久的か一時的かは、そのページが今後も存在するか否かで分類。
 - 恒久的: 新規ドメインや HTTP から HTTPS のページにリダイレクトさせる場合 
 - 一時的: メンテナンス期間などにメンテナンス画面にリダイレクトさせる場合
- メソッドの変更は、初回のリクエストが POST で、次回以降に GET や HEAD を使う場合にユーザーへの確認不要で行えるかどうか。
 - 301/308: リクエストされたページが別の場所に移動したときに使用。Google は検索エンジン側にページ移動を伝える手段として、301 を使うことを推奨。
 - 302/307: 一時的な移動。モバイルサイトへのジャンプやメンテナンスページを表示したりする。
 - 303: リクエスト先に返すべきコンテンツがない、あるいは本来返すべきページが別にある場合、そちらにジャンプさせるために使用。

クライアントは、Location ヘッダーを見て、サイドリクエストを送信し直します。
`curl` コマンドに -L を付与すると、ステータスコード 300 番台かつ Location ヘッダーに値があれば、その指定された URL に再度リクエストを送信します。

リダイレクト回数は、HTTP/1.0 の最初の RFC では、5回までを目安と書かれていたが、RFC2616 の HTTP/1.1 でその制限がなくなりました。
しかし、Google では以下の欠点からリダイレクト回数は5回以下、できれば3回以下というガイドラインが設けられている。
リダイレクトの欠点として、リダイレクト先が別サーバーだった場合、 TCP セッションの接続、HTTP の送受信と2往復の通信が発生します。リダイレクトが増えるほど表示に時間がかかる。

## 1.7 URL (Uniform Resource Locators)
- URL は RFC1738 で、相対 URL は RFC1808 で定義されました。
- URL は URI の一部として RFC1630 で登場している。
- URI には URN(Uniform Resource Name) が含まれている。
- ウェブでは URN が登場することはあまりないので、URI と URL は同意と考えて支障ない。
- RFC3305 で URL は慣用表現、公式表記は URI とされているが、URL の方が一般的。

### 1.7.1 URL の構造
- スキーマ: https
- ホスト名: www.qnote.co.jp
- パス: index.html

URL の全要素が入ったサンプルは以下。  
`スキーマ://ユーザ:パスワード@ホスト名:ポート/パス#フラグメント?クエリー`

- URL に名詞などを使い、URL から内容が推測できるようにする方が、Google の検索順位やユーザにとっても良い。
- HTTP1.0 まで、URL には文字数制限はなかったが、Internet Explorer では 2038 文字までしか扱えず、だいたい 2000 文字が目安とされている。
- HTTP2.0 になって URL が長すぎるとステータスコード 414 URL To Long が追加された。（RFC7231）

### 1.7.2 URL と国際化
- URL のドメイン名は、当初半角英数字とハイフンしか使えなかった。
- 2003年に RFC3492 で国際化ドメイン名（IDN）の表現のためのエンコーディングルール Punycode が決められた。https://punycoder.com/
- ウェブの仕組みで UTF-8 などの文字種がサポートされたわけではなく、日本語や中国語など半角英数字以外が決まったルールで半角英数字に置き換えられ、リクエストが送信される。

## 1.8 ボディ
- HTTP0.9 ではリクエストにデータを含められず、レスポンスはファイルコンテンツそのものだった
- HTTP1.0 ではリクエストとレスポンスにヘッダーが含まれ、ヘッダーとボディを切り分ける必要がある
```sh
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Date: Mon, 09 Dec 2019 22:13:27 GMT
< Content-Length: 32
< Content-Type: text/html; charset=utf-8
< ##### これ以降の Content-Length で指定されたバイト数分がボディ
<html><body>hello</body></html>
* Closing connection 0
```
- 高速化のため、ボディが圧縮される場合があり、この時は Content-Encodeing で指定された圧縮アルゴリズムを使っってボディを展開する必要がある。
- ヘッダーだけ要求する HEAD メソッドでも Content-Length や キャッシュ用の ETag も返す必要がある。
- curl コマンドでは受信時のボディの取得は自動。
- curl コマンドで送信時にボディをサーバーに送信するには、-d を使う。Content-Type は `application/x-www-form-urlencoded` がデフォルト。
```sh
# JSON 送信
$ curl -d "{\"hello\": \"World\"}" -H "Content-Type: application/json" http://localhost:1888

# ローカルに test.json がある場合
$ curl -d @test.json -H "Content-Type: application/json" http://localhost:1888
```

### GET リクエスト時のボディ
- GET や HEAD のリクエストでも curl でボディを送信することができる
```sh
# -X GET で強制的にメソッドを切り替える
$ curl -X GET --data "hello=world" http://localhost:1888

# server.go 側の表示
GET / HTTP/1.1
Host: localhost:1888
Accept: */*
Content-Length: 11
Content-Type: application/x-www-form-urlencoded
User-Agent: curl/7.54.0

hello=world
```
- RFC としては「想定内だが、推奨されない使い方」とされている。
- GET, HEAD, OPTIONS, CONNECT ではペイロードのボディを持つことができるが、サーバ側で拒否される可能性があると書かれている。
- TRACE は「ペイロードのボディは含めてはならない」とされている。
