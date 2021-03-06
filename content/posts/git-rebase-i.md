---
title: "コミットの編集方法"
date: 2020-05-09T07:26:49+09:00
---

コミットメッセージを修正したり、コミットをまとめたり、コミットの順番を入れ替える方法を列挙します。

## コミットメッセージを修正する
### 直前のコミットメッセージを修正する
```sh
$ git commit --amend -m"コミットメッセージ"
```

### 二つ以上前のコミットメッセージを修正する
三つ前にコミットしたメッセージを修正したい場合の例を紹介します。

```sh
$ git rebase -i HAED~3
```

そうすると、以下の様にvim が開く。
古い順に上から並んでいるので、三つ前のコミットは一行目です。

```sh
pick fe03ec9 [add] コンテントネゴシエーションまで
pick 9a3f90a [modify] tags 追記
pick 53d3be4 [add] gitコマンドでブランチ名を修正する方法draft: true
```

一行目の `pick` を `edit` と変更して、新しいコミットメッセージに変更する。

```sh
edit fe03ec9 新しいコミットメッセージ
pick 9a3f90a [modify] tags 追記
pick 53d3be4 [add] gitコマンドでブランチ名を修正する方法draft: true
```

その後、`:wq` で保存するとコミットメッセージが変更される。

## コミットをまとめる
二度に分けたコミットを後で一つにまとめたいという時に使います。

```sh
$ git rebase -i HAED~3
```

vim が開いたら、`pick` を `squash` に変更する。

## コミットの順番を入れ替える
複数人で同じブランチにコミットすると、`pull` の設定によって、履歴が汚くなることがある。そんな時にコミットの順番を整えて履歴を綺麗にしたい時などに使う。

```sh
$ git rebase -i HAED~3
```

vimが開いたら、単純にコミットの順番を入れ替えて保存すれば良い。

## 参考サイト
- https://dev.to/lydiahallie/cs-visualized-useful-git-commands-37p1#rebase
- https://www.granfairs.com/blog/staff/git-commit-fix
