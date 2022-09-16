---
title: "PHPUnit の備忘録"
date: 2022-09-15T09:12:14+09:00
---

以下のクラスのテストのテストを書いていきます。
```php
<?php

namespace  App\Actions\Item;

use App\Models\Item;

class CreateItem
{
    private $cdType = 'TY';

    public function execute(array $input)
    {
        if ($this->isUniqueItemName($input)) {
            return abort(422);
        }

        return Item::create([
            ...$input,
            'item_cd' => $this->generateItemCd(mt_rand()),
        ]);
    }

    public function isUniqueItemName($input)
    {
        $itemName = $input['item_name'];

        return !Item::whereItemName($itemName)->exists();
    }

    private function generateItemCd($number)
    {
        return $this->cdType . $number;
    }
}
```

## モックを使用する
```php
<?php

namespace Tests\UnitActions\Item;

use App\Actions\Item\CreateItem;
use App\Models\Item;
use ReflectionClass;
use Tests\TestCaseWithDb;

class CreateItemTest extends TestCaseWithDb
{
    /**
     * @testWith [true, false]
     */
    public function testCreate(bool $bool)
    {
       $input = Item::factory()->make()->toArray();

       $mock = $this->getMockBuilder(CreateItem::class)
                    ->setMethods(['isUniqueItemName'])
                    ->getMock();
       $mock->expects($this->once())
            ->method('isUniqueItemName')
            ->willReturn($bool);

       try {
           $result = $mock->execute($input);
           $this->assertDatabaseHas('items', $input);
       } catch (\Exception $e) {
           $this->assertTrue($e instanceof \Symfony\Component\HttpKernel\Exception\HttpException);
       }
    }
}
```

- getMockBuilder を使用して、 isUniqueItemName() のメソッドをモック
- expects で該当のメソッドがなんど呼ばれたかのテスト可能
- willReturn に期待する戻り値を指定する
- @testWith は PHPUnit のアノテーションです

## private protected なプロパティやメソッドのテスト
```php
<?php

namespace Tests\UnitActions\Item;

use App\Actions\Item\CreateItem;
use App\Models\Item;
use ReflectionClass;
use Tests\TestCaseWithDb;

class CreateItemTest extends TestCaseWithDb
{
    public function testGenerateItemCd()
    {
        $create = new CreateItem();

        $reflection = new ReflectionClass($create);
        $generateItemCd = $reflection->getMethod('generateItemCd');
        $generateItemCd->setAccessible(true);
        $cdType = $reflection->getProperty('cdType');
        $cdType->setAccessible(true);

        $mun = mt_rand();
        $expected = $cdType->getValue($create) . $mun;
        $result = $generateItemCd->invoke($create, $mun);
        $this->assertSame($expected, $result);
    }
}
```

- ReflectionClass を使用する
- getMethod, getProperty でアクセスしたいメソッド、プロパティを指定
- setAccessible(true) とするとアクセス可能となる
- メソッド実行時は、 get した変数から invoke, invokeArgs を使用する
- プロパティは getValue で値の取得、 setValue で値のセットが可能
