---
layout: post
title: "Lumen 中 Request 要这样拿到 route 中的 parameter"
date: 2016-12-05 17:01:51 +0800
comments: true
categories: 
---

Laravel 中获取 route 中的 parameter 的方法：
```php
//假设 route 是这样的：
$router->resource('posts.comments', PostCommentController::class);

//获取的方法：
$postId = $request->posts;
$commentId = $request->comments;
```

Lumen 中的 routeResolver 有点不太一样，不能这样：
```php
$postId = $request->posts;
```
也不能这样：
```php
$postId = $request->route('posts');
```
报错：`Call to a member function parameter() on array`

所以正确的做法是自己解析：`$request->route()`，略坑。
有人写了一个 helper 的方法：
```php
if (!function_exists('route_parameter')) {
    /**
     * Get a given parameter from the route.
     *
     * @param $name
     * @param null $default
     * @return mixed
     */
    function route_parameter($name, $default = null)
    {
        $routeInfo = app('request')->route();

        return array_get($routeInfo[2], $name, $default);
    }
}
```

就这么用吧。

可能你会问我为什么要自己去 request 中拿 route 的 parameter，因为我在 policy 中要用。
