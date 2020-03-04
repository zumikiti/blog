---
title: "[progit] Git Log のまとめ"
date: 2020-02-17T19:34:09+09:00
tags: [git, progit]
---

## git log のオプション一覧

|オプション | 説明|
|---|---|
|-p | 各コミットのパッチを表示する|
|--stat | 各コミットで変更されたファイルの統計情報を表示する|
|--shortstat --stat | コマンドのうち、変更 / 追加 / 削除の行だけを表示する|
|--name-only | コミット情報の後に変更されたファイルの一覧を表示する|
| --name-status | 変更されたファイルと追加 / 修正 / 削除情報を表示する|
|--abbrev-commit | SHA-1 チェックサムの全体 (40 文字 ) ではなく最初の数文字のみを表示する|
|--relative-date | 完全な日付フォーマットではなく、相対フォーマット (“2 weeks ago” など ) で日付を表示する|
| --graph | ブランチやマージの歴史を、ログ出力とともにアスキーグラフで表示する|
|--pretty | コミットを別のフォーマットで表示する。オプションとして oneline, short, full, fuller そして format ( 独自フォーマットã設定する ) を指定可能|

## 使えそうなコマンドいくつか

```sh
過去 n 回分のコミットのみを表示する
$ git log -3

指定した日付以降のコミットを表示
$ git log --since=2.weeks
$ git log --since="2019-12-24"

指定した日付以前のコミットを表示
$ git log --until=2.weeks
$ git log --until="2019-12-24"

指定した文字列にマッチするコミットのみ表示
$ git log --author=hoge

指定した文字列をコミットメッセージに含むコミットのみ表示
$ git log --grep=fix

指定した文字列をコードを変更削除したコミットのみ表示
$ git log -Shoge
```