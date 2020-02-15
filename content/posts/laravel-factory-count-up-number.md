---
title: "Laravel factory でカウントアップするデータを作成したい"
date: 2020-02-15T22:41:39+09:00
tags: [laravel, factory, facker]
---

データベースの items テーブルに、id とは別に sort を持つテストデータを作ろうと、factory で以下のように設定した。
```php
$factory->define(Item::class, function (Faker $faker) {
    return [
        'sort' => Item::get()->count() + 1,
    ];
});
```

しかし、これでは以下のように sort は全て同じになってしまった。

| id | sort |
|---|---|
|  1 |    1 |
|  2 |    1 |
|  3 |    1 |


sort の値を順にあげて登録するには、シーダーで任意に登録するしかなさそうだ。

```php
for ($i=1; $i < 4; $i++) { 
    factory(Item::class)->create([
        'sort' => $i,
    ]);
}
```
