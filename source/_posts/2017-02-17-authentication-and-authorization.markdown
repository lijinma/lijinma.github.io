---
layout: post
title: "一分钟讲清楚 Authentication 和 Authorization"
date: 2017-02-17 17:01:51 +0800
comments: true
categories: 
---

我们在看文档经常会遇到这两个单词，但是很多人对这两个单词的理解有些模糊，所以我今天给大家简单介绍一下，以后读文档会更舒服一些。

> Authentication 一般指身份验证，又称“验证”、“鉴权”，是指通过一定的手段，完成对用户身份的确认。
> Authorization 一般是授权、委托的意思，向…授予职权或权力许可，批准等意思。

所以你可以看一下 Laravel 的文档

1. [Authentication](https://laravel.com/docs/5.4/authentication) ，主要讲的是和登录相关的内容。
2. [Authorization](https://laravel.com/docs/5.4/authorization) ，主要讲的是权限相关的内容（注意，Gates 和 Policies 都必须是在用户登录后才有意义，如果没有登录态，直接返回 false）。

讲完这个在说一说对应的 Http Status Code：

1. Authentication：未登录的时候调用需要登录的接口，一般使用 401 Unauthorized。
2. Authorization：登录后请求没有权限的接口，一般使用 403 Forbidden。

那么肯定有同学问了，为什么 401 是 Unauthorized 而不是 Unautheticated 呢？这不是坑人吗？事实上，参考 RFC，好像确实是弄错了。

我贴一篇解释，秒懂：[原文链接](http://www.dirv.me/blog/2011/07/18/understanding-403-forbidden/index.html)


>There's a problem with 401 Unauthorized, the HTTP status code for authentication errors. And that’s just it: it’s for authentication, not authorization. Receiving a 401 response is the server telling you, “you aren’t authenticated–either not authenticated at all or authenticated incorrectly–but please reauthenticate and try again.” To help you out, it will always include a WWW-Authenticate header that describes how to authenticate.

>This is a response generally returned by your web server, not your web application.

>It’s also something very temporary; the server is asking you to try again.

>So, for authorization I use the 403 Forbidden response. It’s permanent, it’s tied to my application logic, and it’s a more concrete response than a 401.

>Receiving a 403 response is the server telling you, “I’m sorry. I know who you are–I believe who you say you are–but you just don’t have permission to access this resource. Maybe if you ask the system administrator nicely, you’ll get permission. But please don’t bother me again until your predicament changes.”

>In summary, a 401 Unauthorized response should be used for missing or bad authentication, and a 403 Forbidden response should be used afterwards, when the user is authenticated but isn’t authorized to perform the requested operation on the given resource.
