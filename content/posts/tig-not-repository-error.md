---
title: "ventura にアップグレードしたら、 tig が Not a git repository となってしまった"
date: 2023-01-07T21:13:33+09:00
---

mac の OS を ventura にアップグレードしてみたら、 tig が使えなくなった。

```sh
$ tig
tig: Not a git repository
```

## tig を疑って原因を探る
- .git ディレクトリはもちろんある。試しに clone し直してみても状況同じ。

- git コマンドも使えるし、 tig も使用はできる。

```sh
$ git -v
git version 2.39.0

$ tig -v
tig version 2.5.7
ncursesw version 6.3.20211021
readline version 8.2
```

- git と tig の再インストールもしてみたが、改善なし。。

- git init で作った場合もだめ

```sh
$ git init && git add . && git commit -m 'first commit'          Sat Jan  7 21:42:59 2023
Reinitialized existing Git repository in /Users/akihiro/code/test-tig/.git/
[master (root-commit) 37a4c03] first commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.md

$ tig                                                            Sat Jan  7 21:43:03 2023
tig: Not a git repository

$ git log                                                    Sat Jan  7 21:43:06 2023
commit 37a4c03336db2494657ef1eecd740c4fa90dd287 (HEAD -> master)
Author: akihiro <aponmaru@gmail.com>
Date:   Sat Jan 7 21:43:03 2023 +0900

    first commit
```

- tig のソースコードをみてみる

tig.c の 838 行目で返してるのかな？。

```c
	/* Require a git repository unless when running in pager mode. */
	if (!repo.git_dir[0] && request != REQ_VIEW_PAGER)
		die("Not a git repository");
```

https://github.com/jonas/tig/blob/master/src/tig.c#L838


## tig が問題ではなく、 tmux が問題の様子

普段 tabby を使っているが、試しに iterm2 で開いてみたら普通に tig が使えた。え！tabby のせいなの？！と思い、tabby のバージョンアップとか調べたが、 iterm2 で tmux 起動したら tig が同様の事象で使えなかった。

tmux でうまくディレクトリがうまく読めていないのだろうかと思われる。

ただ、どう確認したらいいかが分からない。。。

tmux のアップデートやプラグインのアップデートをしてはみたが改善なし。

tig も tmux もメインで使っているから困るんよね〜何でだろう。。。

（続く...）
