---
layout: post
title: "讲讲我最近做的一个 App 后端项目"
date: 2015-3-22 21:21:51 +0800
comments: true
categories: 
---

以前主要做网站类的后端，没有深入做过一个 App 的后端。最近因为前公司项目解散加入了一个新的公司，现在做的项目是一个教育导航 App 的后端 Api 部分。

因为只有我一个后端程序员，所以一切要使用的框架等都需要我来调查并选择。我用的是 PHP，以前的项目使用过 Codeigniter，也使用过非常轻量级的 Slim 框架，Laravel 也有一直关注，看了 Jeffery Way 的 Laracasts 很多视频，确实感觉到了 Laravel 在快速建立方面非常优秀，而且因为使用的人越来越多，现在也有非常丰富的第三方库，而且 Laravel 框架一直都在使用 PHP 最新的一些技术，这也是我喜欢这个框架的一个原因。

<!-- more -->

确实因为对 Laravel 框架的偏好，打算使用最近发布的 Laravel 5.0，因为我是为 App 后端开发 Api，所以需要在对应框架上找一个方便的 Api 解决方案，Laravel 中比较优秀的 Package 是https://github.com/dingo/api ，Summer 也推荐这个框架（参考讨论https://phphub.org/topics/504），但是一个最大的问题是，dingo/api 还没有 Laravel 5.0 的版本（参考讨论https://github.com/dingo/api/issues/213）。

思前想后，dingo/api 确实在很多方面都很全面，也很方便（参考https://github.com/dingo/api/wiki），比如集成了fractal来对数据进行统一处理 （参考https://github.com/thephpleague/fractal）, 比如集成了多种 Authetication，比如集成了接口访问频次的限制（Rate limiting），比如集成了多种错误 Response 等，总体来说如果使用这个 Package 会节省很多时间。

因为要使用 dingo/api，最后的决定使用 Laravel 4.2，正好也了解一下 Laravel 4.2 的特性，因为我确实没有使用 Laravel 框架做过正式的项目，将来再升级 Laravel 5.0 也行。

中间遇见的一个坑和解决方案：
【坑】：在 Api 中使用 Session。
在 Api 开发中对 Session 定位不准，因为 Api 是无状态且没有 Cookie 的支持，如果想使用 Session，那就必须通过 Headers 或Query string 或 Post parameters 来传送Session ID，我当时考虑到有了 Session ID，我不但能使用 Session，而且Authetication 部分直接就可以使用 Laravel 的 Auth了，岂不是一举两得。

后来发现使用 Session 是不好的解决方案，因为 Session 会过期，如果 Authetication 使用 Session 来处理，在授权方面就完全没有 Oauth 2.0那么灵活，另外一个问题就是，App的登录一般都是永久的登录，使用 Oauth 2.0可以通过 refresh token 来解决，但是使用 Session 你就态尴尬了。

解决方案：需要用到 Session 的地方使用 Cache 来解决（参考https://phphub.org/topics/594），Summer 还给出了我具体的解决方案，非常感谢。Authentication 部分不使用 Session 就直接使用 Oauth 2.0（参考https://github.com/dingo/api/wiki/Authentication 和https://github.com/lucadegasperi/oauth2-server-laravel/wiki），Oauth 2.0在安全方面和灵活方面真的是爽到爆，当然需要 Https 的支持。

几个小技巧分享：

（1）在线上环境，如果数据库需要一些初始数据，可以使用 migration 完成，不使用 Seed 主要有两个原因，一、Seed 会依赖一些 开发环境的包 ，例如 faker 等，而这些包是在 require-dev 中，二， 使用 Seed 可能会和开发环境的 Seed 混淆。
（2）重写别人 Trait 的方法：
```php
trait CustomCommanderTrait
{
    use CommanderTrait {
        mapInputToCommand as originMapInputToCommand;
     ...
    }
```php



 Api 的文档选择

选了很多，最后决定使用http://apidocjs.com/，他的优点就是安装使用都很方便，另外一个优点是可以直接在 Api 文档中测试，省去使用 Postman 这样的工具（参考http://apidocjs.com/example/），不过里面有一个坑，就是测试 Api 的部分代码如下：

```php
// send AJAX request, catch success or error callback
$.ajax({
    url: url,
    dataType: "json",
    contentType: "application/json",
    data: JSON.stringify(param),
    headers: header,
    type: type.toUpperCase(),
    success: displaySuccess,
    error: displayError
});
```

在 Oauth 2.0获取 Access token 的时候，提交方式必须是：application/x-www-form-urlencoded，所以我使用 gulp 替换了对应的内容为：

```php
// send AJAX request, catch success or error callback
$.ajax({
    url: url,
    data: param,
    headers: header,
    type: type.toUpperCase(),
    success: displaySuccess,
    error: displayError
});
```

jQuery Ajax 默认 Post 的方式就是application/x-www-form-urlencoded，也是醉了。


以上是我这段时间的一些分享，将来不定期的更新，如果有错的地方，或者低效的地方，还请大家指出来，多谢。

最后要感谢下 https://phphub.org 和里面热心的小伙伴，我一般没有头绪的问题都在这里进行提问，学到了很多，非常感谢。

