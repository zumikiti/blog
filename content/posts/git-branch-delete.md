---
title: "ローカルブランチを削除する方法"
date: 2020-05-10T10:13:13+09:00
---

## ローカルブランチを削除する
```sh
$ git branch -D ブランチ名
```

## マージ済みのローカルブランチを削除する
```sh
$ git branch --merged | grep -v '*' | xargs -I % git branch -D %
```

## 全てのローカルブランチを削除する
```sh
$ git branch | grep -v '*' | xargs -I % git branch -D %
```

