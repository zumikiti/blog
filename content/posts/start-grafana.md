---
title: "Grafana をちょっと触ってみた"
date: 2023-01-09T09:38:45+09:00
---

Grafana とは Kibana と同様にサーバ監視用のダッシュボードツールのようだ。

とりあえず、 Mac へのインストールと起動、ログインまでを試してみる

## Mac への Grafana のインストール
Mac であれば、 homebrew でインストールが可能

```sh
brew update
brew install grafana
```

ここで、以下のエラーが出たが後続の処理はできたので一旦無視

`Error: Permission denied @ apply2files - /usr/local/lib/docker/cli-plugins`

## 起動
```sh
brew services start grafana
```

```sh
==> Tapping homebrew/services
Cloning into '/usr/local/Homebrew/Library/Taps/homebrew/homebrew-services'...
remote: Enumerating objects: 2311, done.
remote: Counting objects: 100% (245/245), done.
remote: Compressing objects: 100% (107/107), done.
remote: Total 2311 (delta 157), reused 181 (delta 138), pack-reused 2066
Receiving objects: 100% (2311/2311), 640.22 KiB | 14.23 MiB/s, done.
Resolving deltas: 100% (1037/1037), done.
Tapped 1 command (45 files, 804.6KB).
==> Successfully started `grafana` (label: homebrew.mxcl.grafana)
```

これで、 http://localhost:3000 でアクセスできるはず。

なお、停止したい場合は

```sh
brew services stop grafana
```

## ログイン
初期はIDもPWも `admin` でログインできる。

## まとめ
一旦時間的制約でここまで、後日少しづつ設定をいじって、自宅の録画サーバーの可視化をしていきたいと思う。

## 参考
- https://grafana.com/docs/grafana/latest/setup-grafana/installation/mac/
