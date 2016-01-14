---
layout: post
title: "【博客优化】使用 InstantClick"
date: 2016-01-14 11:32:55 +0800
comments: true
categories: 博客优化
---


## 什么是 InstantClick

网址：http://instantclick.io/

InstantClick 在某些时刻使用提前加载网页的策略来减少打开一个链接的等待时间(latency)，这些时刻包括打开一个链接前： `mouseover`(hover)，`mousedown`，`touchstart`(移动设备上)等。

<!-- more -->

## 如何使用 InstantClick

网址：http://instantclick.io/download

下载好 `instantclick.min.js`，

在网页的最后边，通常是 `</body>` 前添加如下代码：

```html
<script src="instantclick.min.js" data-no-instant></script>
<script data-no-instant>InstantClick.init();</script>
</body>
</html>
```

## 如果产品使用 InstantClick，使用黑名单的方式还是白名单的方式？

1. 黑名单的方式；

```html
<script src="instantclick.min.js" data-no-instant></script>
<script data-no-instant>InstantClick.init();</script>
```

如果按照上面的步骤添加了代码，这个时候所有的链接都会采用提前加载的方式进行。如果由于特殊原因，某些链接不想使用提前加载的方式，请添加 data-no-instant

```html
<ahref="/blog/"data-no-instant>Blog</a>
```


2. 白名单的方式；

```html
<script src="instantclick.min.js"data-no-instant></script>
<script data-no-instant>InstantClick.init(true);</script>
```

```html
<a href="/blog/" data-instant>Blog</a>
```

我更建议比较大型的项目使用白名单的方式来使用 InstantClick，为什么呢？

因为很多链接是不能使用提前加载的方式的，比如“退出”链接，“切换语言”链接，触发 javascript 操作的链接。在比较大型的项目中，直接所有链接使用提前加载的方式是很冒险的，至少我在我们项目中是冒险了一次，很多链接考虑不周到。后来也从黑名单的方式改成了白名单的方式。

使用白名单的方式，建议可以通过 nginx 日志来统计链接被访问的次数，访问多的链接优先处理。


## 如果产品使用 InstantClick 需要注意什么？

1. 如何跟踪资源的更新？

网址：http://instantclick.io/assets-changes

使用data-instant-track 标记来完成，大型的项目这个地方一定要注意，会有坑。

2. 如何自定义 progress bar

网址：http://instantclick.io/progress-bar

现在还是一个假的 progress bar，所以我的博客直接隐藏掉了，隐藏了更舒服一点。

我的博客已经使用了 `InstantClick`，可以体验下，是不是快到爆炸，哈哈哈。