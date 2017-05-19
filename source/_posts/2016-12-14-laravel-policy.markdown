---
layout: post
title: "讲讲我对 Laravel Policy 的认识"
date: 2016-12-14 17:01:51 +0800
comments: true
categories: 
---

公司的项目中 Restful 的接口对应的资源的权限都使用 Laravel Gate Policy 做的，一般权限做了如下的限制：
* 创建
* 查看
* 更新
* 删除

这几个权限使用 Policy 可以很容易做出来，比如下面官网的例子，定义了 update 的 alibity：
```php
<?php

namespace App\Policies;

use App\User;
use App\Post;

class PostPolicy
{
    /**
     * Determine if the given post can be updated by the user.
     *
     * @param  \App\User  $user
     * @param  \App\Post  $post
     * @return bool
     */
    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }
}
```
Controller 中使用如下：

```php
<?php

namespace App\Http\Controllers;

use App\Post;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class PostController extends Controller
{
    /**
     * Update the given blog post.
     *
     * @param  Request  $request
     * @param  Post  $post
     * @return Response
     */
    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);

        // The current user can update the blog post...
    }
}
```

## 那问题来了，如果我想做资源列表(/posts)的权限怎么做？

我没进行思考，就直接想加到 policy 里面。

```php
    //PostPolicy
    ...
    public function viewList(User $user, array $posts)
    {
        //todo
    }
    ...
    //PostController
    ...
    $this->authorize('viewList', $posts);
    ...
```
发现不行啊，细细的查看了 Gate 的实现，给你看一个函数你就懂了：
```php
//Gate.php
    protected function firstArgumentCorrespondsToPolicy(array $arguments)
    {
        if (! isset($arguments[0])) {
            return false;
        }

        if (is_object($arguments[0])) {
            return isset($this->policies[get_class($arguments[0])]);
        }

        return is_string($arguments[0]) && isset($this->policies[$arguments[0]]);
    }
```
在 authorize 的时候，Gate 会通过第一个参数（除去 user）来判断是哪个对象，知道了这个对象，才能找到对应的 policy class，所以一般我们这么用：

```php
$this->authorize('update', $post);
$this->authorize('create', Post::class);
```
如果你想用 viewList，可以这么做：

```php
    //PostPolicy
    ...
    public function viewList(User $user, $class, array $posts)
    {
        //todo
    }
    ...
    //PostController
    ...
    $this->authorize('viewList', Post::class, $posts);
    ...
```
是不是觉得很变扭，$class 这个参数我们根本就没有用，回过头想一想，这么做好像哪里不太对。。。。。。。。。。。

## 是的，Policy 本来就是针对单个对象的，我现在要控制列表的权限，正确的做法应该是这样的：

```php
        \Gate::define('view-post-list', function ($user, $posts) {
            //
        });
```
使用
```php
    //PostController
    ...
    $this->authorize('view-post-list',  $posts);
    ...
```
查看 Gate 的代码，你会发现，他会优先 check policy 是否存在，不存在就会去检查是否有定义的 abilities，如果有就会使用，贴一段代码你就懂了：

```php
    protected function resolveAuthCallback($user, $ability, array $arguments)
    {
        if ($this->firstArgumentCorrespondsToPolicy($arguments)) {
            return $this->resolvePolicyCallback($user, $ability, $arguments);
        } elseif (isset($this->abilities[$ability])) {
            return $this->abilities[$ability];
        } else {
            return function () {
                return false;
            };
        }
    }
```

## 我猜你已经懂了我的意思，若没懂，请留言。