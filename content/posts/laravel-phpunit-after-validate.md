---
title: "Laravel のバリデーションの After フックをテストする"
date: 2024-03-20T09:20:40+09:00
draft: true
---

以下のようなバリデーションがあったとする。
after フックを単体テストを行いたい場合の備忘録。

```php
<?php

class UserFrom extends FormRequest
{
    public function rules()
    {
        return [
            'first_name' => 'required',
            'last_name'  => 'required',
        ];
    }

    public function withValidate($validator)
    {
        $validator->after(function($validator) {
            $this->userNameValidate($validator);
        });
    }

    /**
     * ユーザー名が空白文字含めて51文字いないかのチェック
     */
    private function userNameValidate($validator)
    {
        $firstName = $this->input('first_name');
        $lastName = $this->input('last_name');

        $userName = "{$lastName} {$firstName}";

        $count = mb_strlen($userName);

        if ($count > 51) {
           $validator->errors()->add(
               'last_name',
               'ユーザー名は、50文字以内で入力してください。'
            );
        }
    }
}
```

```php
<?php

class UserFormTest extends TestCase
{
    public function roles()
    {
        $this->request = new UserFrom();
        $this->request->setRouteResolver(function() {
            $mock = Mockery::mock('Illuminate\Routing\Route')
                ->shouldReceive('parameter')
                ->andReturn()
                ->getMock();

            return $mock;
        }); 

        $rules = $this->request->rules();

        return $rules;
    }

    private function makeInput(array $input = [], string $exceptKey = null)
    {
        $baseInput = [
            'first_name' => Str::random(25),
            'last_name' => Str::random(25),
        ];

        $input = array_replace($baseInput, $input);
        $input = Arr::except($input, $exceptKey);

        return $input;
    }

    /**
     * @dataProvider formData
     */
    public function testFormValidation($expected, ...$inputs)
    {
        $rules = $this->rules();
        $input = $this->makeInput(...$inputs);

        $validator = Validator::make($input, $rules);
        $this->request->merge($input);
        $this->request->withValidator($validator);

        $result = $validator->passes();
        $this->assertSame($expected, $result);
    }

    public function formData()
    {
        return [
            'ユーザー名が51文字' => [
                false,
                [
                    'first_name' => Str::random(26),
                ],
            ],
        ];
    }
}
```
