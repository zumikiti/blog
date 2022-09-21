---
title: "git config push.autoSetupRemote"
date: 2022-09-21T08:45:14+09:00
---

git 2.37 から、`push.autoSetupRemote` が追加された。

https://dev.to/this-is-learning/this-new-git-push-config-will-save-you-lot-of-frustration-27a9

## 何がいいのか
現状だとプッシュ時に、リモートブランチを作成する場合、 `--set-upstream` オプションを求められる。これが不要になる。
```sh
$ git push
fatal: The current branch strime has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin strime
```

## 設定方法
まずは、git のバージョンが 2.37 以降か確認して、そうでなければ、 update する。
```sh
$ git --version
git version 2.32.1 (Apple Git-133)
```

```sh
$ brew instal git 

$ git --version
git version 2.37.3
```

2.37 以降になったら、以下コマンドで設定可能。
```sh
$ git config --global push.autoSetupRemote true
```
