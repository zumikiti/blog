---
title: "Laravel バリデーションで該当モデルの情報を取得する方法"
date: 2020-02-14T14:55:48+09:00
tags: [laravel, php]
---

Laravel バリデーションで unique に ignore で該当 id のみ除外したかったが、該当の id の取り方がわからなかった。

```php
$this->route(`user`);
```

これだけ。

これは、`Illuminate\Http\Request::route` を使用している。

```php
    /**
     * Get the route handling the request.
     *
     * @param  string|null  $param
     * @param  mixed  $default
     * @return \Illuminate\Routing\Route|object|string|null
     */
    public function route($param = null, $default = null)
    {
        $route = call_user_func($this->getRouteResolver());

        if (is_null($route) || is_null($param)) {
            return $route;
        }

        return $route->parameter($param, $default);
    }
```


