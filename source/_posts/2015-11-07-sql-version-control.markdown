---
layout: post
title: "关于数据库建表脚本管理"
date: 2015-11-07 21:30:21 +0800
comments: true
categories: 
---

这是一个好问题，经过一段时间的使用，我推荐使用 MySQLWorkbench 来进行管理数据库脚本。

我简单分析一下我用过的几种管理方式的优缺点：

##SQL 脚本

优点：可版本管理。
缺点：数据库表修改后，同步数据库比较麻烦。

##Migration（比如 Laravel 的 Migration）

优点：可版本管理，直接使用代码代替 SQL 脚本；
缺点：只能特定的框架才能用，不够通用。

##MySQLWorkbench

优点：修改方便，管理方便，同步数据库非常方便。
缺点：无法版本管理，因为是一个二进制文件。

MySQLWorkbench的亮点功能是：同步建表 Model 和数据库，当Model 修改后，直接和数据库进行同步，生成差异的 SQL 脚本执行，非常方便。


综上，没有使用过 MySQLWorkbench 的同学，真的可以试一试。
