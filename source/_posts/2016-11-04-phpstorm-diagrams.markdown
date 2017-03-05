---
layout: post
title: "如何使用 PhpStorm 查看类的继承关系"
date: 2016-11-04 17:01:51 +0800
comments: true
categories: 
---

今天一个同事教了我一个很有用的小技巧，通过 PhpStorm 查看类的集成关系。

场景是这样的，我使用 Guzzle 要调用一些 api，api 出错了我需要处理 Exception，这个时候我肯定会看一下 Guzzle 的文档，不过看文档稍微有点繁琐，如果你使用 PhpStorm，有更简单的办法：

第一步：选中 Guzzle 的 Exception 文件夹

![file](https://dn-phphub.qbox.me/uploads/images/201611/04/77/RE2o4xSfRP.png)

第二步：选中 Show Diagram

![file](https://dn-phphub.qbox.me/uploads/images/201611/04/77/ClXOGgxeTV.png)

结果：

![file](https://dn-phphub.qbox.me/uploads/images/201611/04/77/BFKxtT5Mqg.png)

是不是很神奇？ 这个时候，我大概 Catch RequestException 来处理一下比较合理。

感谢郭师傅。