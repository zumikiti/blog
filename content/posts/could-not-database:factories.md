---
title: "composer install 実行時に、database/factories のエラーが出た"
date: 2020-01-14T09:43:12+09:00
draft: true
---

`composer install` を実行したところ、`autoload.php` の作成時に以下エラーが表示された。

`Could not scan for classes inside "database/factories" which does not appesr to be file nor a folder`

{{< figure src="/img/could-not-database-factories.png" alt="コマンドラインのエラー" >}}

## 解決方法
解決方法はいたって単純で、`database/factories` というディレクトリを作成すればOK。

```sh
$ mkdir database/factories
```

