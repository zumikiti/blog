---
title: "localhost に別PCからアクセスする方法（homestead）"
date: 2020-08-06T15:08:43+09:00
tags: [homestaed, laravel, vagrant]
---

## homestead.yaml のネットワークインターフェイスを設定する
今回は、固定である必要がないため以下記載でOK
```
networks:
    - type: "public_network"
    - bridge: "en1: Wi-Fi (AirPort)"
```

固定IPにしたい場合は、`ip` を追加すれば良い

`homestead reload --provision` でyaml の設定を読み直す

以下のような質問を受ける。
```
$ homestead reload --provision
==> homestead: Checking if box 'laravel/homestead' version '9.5.1' is up to date...
==> homestead: Clearing any previously set network interfaces...
==> homestead: Specific bridge 'en1: Wi-Fi (AirPort)' not found. You may be asked to specify
==> homestead: which network to bridge to.
==> homestead: Available bridged network interfaces:
1) en1: Wi-Fi (Wireless)
2) en0: Ethernet
3) en2: Thunderbolt 1
4) en3: Thunderbolt 2
5) bridge0
6) p2p0
7) awdl0
==> homestead: When choosing an interface, it is usually the one that is
==> homestead: being used to connect to the internet.
==> homestead:
    homestead: Which interface should the network bridge to? 1 // <- '1' を選択してenter
```

## ip 確認
今回は、固定IP出ないため、IPの確認が必要です。

```
$ homestead ssh 
vagrant@homestead: $ ipconfig
```

`ipconfig` で自分のローカルネットワークと一致するIPがあればそれです。

## 確認したIPにブラウザでアクセス
先程確認したIPをブラウザで表示してみて正しく表示されればOKです。
