---
title: "homestead をアップデートしたら、'Uncaught RuntimeException: A facade root has not been set. in'とエラーが画面上に表示されるようになった"
date: 2020-07-01T14:35:26+09:00
---

## 事象
- サイトを表示すると、`Uncaught RuntimeException: A facade root has not been set. in~` とエラー画面が表示される。
- homestead で `php artisan` コマンドを実行すると、 `Unable to load the "app" configuration file.` と表示される。

## 事象に至る経緯
1. homestead のディレクトリで vagrant destory
2. release ブランチで git pull
3. vagrant box update
4. vagrant up

## 解決方法
### 1. VirtualBox のバージョンを最新にする。
この当時の最新の homestead は、 VirtualBox 6 でないと動作しない。
- VirtualBox のダウンロードは、 https://www.virtualbox.org/wiki/Downloads
- vagrant も更新が必要な場合は、 https://www.vagrantup.com/downloads.html

### 2. VM を作り直す。(1 でダメなら)
1. vagrant box remove laravel/homestead で削除
2. 1 でバージョンが複数あるので指定するように言われたら
  - `--all` で全て消す
  - `--box-version [バージョン番号]` で該当バージョン削除
3. vagrant up

## 参照元
-  https://github.com/laravel/homestead/issues/1219#issuecomment-508906446
-  https://trueman-developer.blogspot.com/2019/06/homesteadphplaravel.html
-  https://qiita.com/ma_me/items/06e61d3e0a174535281e
