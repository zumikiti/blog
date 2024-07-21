---
title: "zoxide でディレクトリを飛び回る"
date: 2024-07-21T11:23:21+09:00
---

ubuntu + fish での [zoxide](https://github.com/ajeetdsouza/zoxide) のセットアップ手順です。

## 1. zoxide のインストール
```sh
curl -sSfL https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | sh
```

mac などでは、 brew でもインストールできる。
詳しくは、[readme](https://github.com/ajeetdsouza/zoxide?tab=readme-ov-file#installation) を確認してください。

## 2. fzf のインストール
fzf を使用しているので、インストールする。

```sh
sudo apt install fzf
```

## 3. config.fish に設定追加
以下では、 zoxide の設定読み込みと、j コマンドで zi のエイリアスを設定。

```sh
zoxide init fish | source
function j --wraps=zi --description 'alias j=zi'
  zi $argv;
end
```

## 4. デモ
これで、以下のように、過去に訪れたディレクトリに `j` でジャンプすることができる。

![demo](/img/zoxide-demo.gif)
