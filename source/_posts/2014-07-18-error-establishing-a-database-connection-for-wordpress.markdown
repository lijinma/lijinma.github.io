---
layout: post
title: "WordPress 出现“Error establishing a database connection ”（无法连接数据库）怎么办？"
date: 2014-07-18 04:54:25 +0800
comments: true
categories: 
---

今天朋友使用 WordPress 建站的时候出现“无法连接数据库”的问题，我想分享我解决问题的思路。

##（1)定位问题：获取更详细出错信息。

看到这个提示后，我们脑子里会有多重假设，如果靠着我们的假设会花费大量的时间去试验，我的做法是：WordPress 是否有一种更详细显示出错信息的开关？在我寻找后，我发现，在`wp-config.php`中有一个可以修改为`define('WP_DEBUG', true)`;

<!-- more -->

## (2)分析问题：确定解决方案

打开`DEBUG`的开关后，发现问题是`Can't connect to local MySQL server through socket '/var/run/mysqld/mysql.sock’`

问题很明显是 PHP 无法通过默认的`mysql.sock`连接到 MySQL server。

### 2.1 确定 mysql.sock 的位置

使用指令 `mysql -uroot -p` 进入 MySQL；
使用指令 `SHOW VARIABLES LIKE 'socket’;` 获取到 mysql.sock 的位置；

```
| socket        | /tmp/mysql.sock |
```

## (3)解决问题：用最简单好用的方法

一般出现上述问题有几种方法来解决：

（1）软连接到默认的 sock 位置 `ln -s /tmp/mysql.sock /var/run/mysqld/mysql.sock`

这是最简单最快的执行方法。

（2）修改 php.ini 中的 `mysql.default_socket=tmp/mysql.sock`

使用 `php -i|grep php.ini`找到 php.ini 的位置，然后修改，但是这种方法需要重启 Apache 或者 Nginx。

（3）直接修改 wp-config.php  `define('DB_HOST', 'localhost:/tmp/mysql.sock');` 在 WordPress 应用中配置 sock 的位置。

这种方法可以直接在应用层面修改，不涉及后台设置，如果没有太多权限，这是一种不错的方法。

最后使用的是方法（3），原因是朋友的后台账号没有足够的权限进行方法（1）和（2）的操作。




