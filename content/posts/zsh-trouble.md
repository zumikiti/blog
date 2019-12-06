---
title: "Zsh の始めたら、問題がいくつか"
date: 2019-12-06T07:57:39+09:00
draft: false
tags: [zsh, Sublime Text]
---

## 既設の alias コマンドが効かない
`.bash_profile` に設定していた alias のコマンドが効かない。
当たり前、`Zsh` にしたら `.zshrc` 等が読み込まれるので、`.zshrc` か `.zprofile` などに alias を書く必要がある。

## Sublime Text 3 の gofmt でエラー
{{< highlight shell >}}
Traceback (most recent call last):
  File "/Users/zumikiti/Library/Application Support/Sublime Text 3/Installed Packages/Gofmt.sublime-package/gofmt.py", line 257, in run_formatter
    formatter = Formatter(view)
  File "/Users/zumikiti/Library/Application Support/Sublime Text 3/Installed Packages/Gofmt.sublime-package/gofmt.py", line 166, in __init__
    self.cmds = [Command(cmd, self.view, self.window) for cmd in cmds]
  File "/Users/zumikiti/Library/Application Support/Sublime Text 3/Installed Packages/Gofmt.sublime-package/gofmt.py", line 166, in <listcomp>
    self.cmds = [Command(cmd, self.view, self.window) for cmd in cmds]
  File "/Users/zumikiti/Library/Application Support/Sublime Text 3/Installed Packages/Gofmt.sublime-package/gofmt.py", line 77, in __init__
    self.window)
  File "/Users/zumikiti/Library/Application Support/Sublime Text 3/Packages/golangconfig/all/golangconfig.py", line 227, in subprocess_info
    raise exception
golangconfig.EnvVarError: The following environment variable is currently unset: GOPATH
{{</ highlight >}}

`GOPATH` を `~/.zprofile` に設定する必要がある。

{{< highlight shell >}}
$ which go
/usr/local/go/bin/go

$ vi ~/.zprofile
{{</ highlight >}}

{{< highlight shell >}}
# ~/.zprofile
export GOPATH=/usr/local/go/bin/go
{{</ highlight >}}

## 参考
- [go - GOPATH not found error from the Gofmt plugin within SublimeText - Stack Overflow](https://stackoverflow.com/questions/58566612/gopath-not-found-error-from-the-gofmt-plugin-within-sublimetext)
- [.zshrcなどのzshの設定ファイルの場所を移す - 計算物理屋の研究備忘録](http://keisanbutsuriya.hateblo.jp/entry/2015/02/03/235603)