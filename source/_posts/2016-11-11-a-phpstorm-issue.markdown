---
layout: post
title: "PhpStorm 的一个小坑：配置记录问题"
date: 2016-11-11 17:01:51 +0800
comments: true
categories: 
---

一般创建一个 Class 的时候，是这样的：
![file](https://dn-phphub.qbox.me/uploads/images/201611/11/77/F2vqxzECt3.png)

项目创建后，第一次在敲 Namespace 的时候，不小心敲错了，本来应该是 `App`，结果变成 `app`，

每次创建的时候我都需要手动修改一下，PhpStorm 记住了第一次添加的 Namespace

那今天我创建了一个 Event 和 Listen （忘记修改 `app` -> `App`）后，添加好了对应的 ServiceProvider 后，发现 Listen 怎么都无法触发，Debug 所有的 Listeners 也没有发现。

后来细致检查代码才发现，真是自己坑了自己。

解决这个问题就是：删除 `rm -rf .idea`

这样之前写错的 Namespace 就没有了。

![file](https://dn-phphub.qbox.me/uploads/images/201611/11/77/Dwr4bBGdIy.png)

你们有遇见类似这样抓狂的问题吗？我感觉每次都是我自己挖坑，然后埋自己。
