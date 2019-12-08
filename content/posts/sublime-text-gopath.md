---
title: "Sublime Text で GOPATH のエラーが出た場合の対処法"
date: 2019-12-09T07:55:53+09:00
draft: false
tags: [SublimeText, Golang, go] 
---

## エラー内容
Sublime Text で go を書いていたら、保存時に以下のエラーが出てきた。
```
golangconfig.EnvVarError: The following environment variable is currently unset: GOPATH
```

## 対処法
[sublime-config/user.md at master · golang/sublime-config](https://github.com/golang/sublime-config/blob/master/docs/user.md) を参照し、Golang Config に以下の設定を追記する。

```json
{
    "PATH": "/Users/jsmith/go/bin",
    "GOPATH": "/Users/jsmith/go"
}
```
