---
title: "公開鍵を毎回聞かれる場合の対応"
date: 2020-04-21T19:35:01+09:00
---

git コマンドを実行する際に、毎回公開鍵の入力を求められることがある。
```sh
$ git fetch -p
Enter passphrase for key '/Users/zumi/.ssh/id_rsa':
```

対処としては、`ssh-keygen -p` を実行する
```sh
$ ssh-keygen -p
Enter file in which the key is (/Users/zumi/.ssh/id_rsa): (enter)
Enter old passphrase: (公開鍵生生時のパスフレーズ入力)
Key has comment 'zumi@hoge.co.jp'
Enter new passphrase (empty for no passphrase): (enter)
Enter same passphrase again: (enter)
Your identification has been saved with the new passphrase.
```

これで、次回以降パスフレーズの入力を毎回聞かれずに済む。
