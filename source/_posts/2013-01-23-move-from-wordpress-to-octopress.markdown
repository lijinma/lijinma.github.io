---
layout: post
title: "博客搬家：从Wordpress迁移到octopress"
date: 2013-01-23 23:59
comments: true
categories: 
---

<img src="/images/post/octopress.jpeg" alt="Octopress" target="_blank" style="float:right;width:150px;margin-left:20px;"  />

终于还是下定决心从Wordpress迁移到Octopress上了，Wordpress的便利导致了他越来越庞大的身躯，但他的臃肿常常会打消我写文章的念头，也真的是不想再花心思在Wordpress主题和插件上了，我们要更专注于写而不是别的花里胡哨的东西，从复杂中解脱，回归简单。
<!--more-->
##为什么使用放弃Wordpress而转向Octopress?

1. Wordpress是动态的，加载慢，尽管我的服务器是Amazon EC2在日本的主机，但是在国内访问依然不快，然后Octopress是静态页面，自然要快一些；
2. 不喜欢Wordpress发布文章的编辑器，喜欢用markdown来文章，这样子我会更专注于内容，而不是格式；
3. Amazon EC2的 Instance偶尔mysql会宕机，导致Wordpress无法访问；
4. [www.stdyun.com](http://www.stdyun.com)免费提供Octopress托管，只需要一个域名就可以搭建网站，而Amazon EC2是要收费的，而且不便宜；

##什么是Octopress?

Wordpress大家都知道，但是Octopress可能只有程序猿比较了解，

>Octopress is a framework designed by [Brandon Mathis](http://brandonmathis.com/) for [Jekyll](http://github.com/mojombo/jekyll), the blog aware static site generator powering [Github Pages](http://pages.github.com/). To start blogging with Jekyll, you have to write your own HTML templates, CSS, Javascripts and set up your configuration. But with Octopress All of that is already taken care of. Simply [clone or fork Octopress](https://github.com/imathis/octopress), install dependencies and the theme, and you’re set.

##部署Octopress需要什么技能？

1. 你需要懂一些基本的git知识，了解github；
2. 你需要熟悉一门轻量级标记语言,比如markdown； 如果你不熟悉markdown，请参考阳志平的文章：[Markdown写作浅谈](http://www.yangzhiping.com/tech/r-markdown-knitr.html)，你就会明白markdown是多么的简单迷人。

##如何迁移wordpress的文章到Octopress？
我的博客本来就没有多少文章，所以我直接手动迁移过来，如果你的文章非常多，请参考大眼夹的文章[从Wordpress迁移到Octopress](http://blog.dayanjia.com/2012/04/migration-to-octopress-from-wordpress/)，讲得非常清楚;

##如何使用stdyun的octopress的免费托管服务？

使用github pages很容易部署一个免费的网站，但是介于github在中国经常被屏蔽的缘故，我选择了stdyun提供的octopress免费托管服务，在中国访问速度非常之快，托管介绍请参考：[为中国的hacker准备的octopress托管](https://blog.stdyun.com/octopress)，如何部署请参考文章：[octopress+stdyun=stdyun octopress托管服务](http://blog.stdyun.com/2012/12/27/octopress-plus-stdyun-equals-stdyun-octopress-tuo-guan-fu-wu/).

##如何使用github pages部署octopress？
我没有部署在github上，所以请大家在网上搜索资料，推荐参考阳志平的文章：[Ruby开源项目介绍(1)：octopress——像黑客一样写博客](http://www.yangzhiping.com/tech/octopress.html).

>有任何与octopress相关的问题，请留言，我会尽可能帮助大家。