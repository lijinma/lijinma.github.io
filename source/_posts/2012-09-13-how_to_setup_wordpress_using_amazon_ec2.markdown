---
layout: post
title: "使用Amazon EC2搭建WordPress全过程"
date: 2012-09-13 01:24
comments: true
categories: 
---

<img src="/images/post/amazon_ec2.jpg" alt="Amazon EC2" target="_blank" style="float:right;width:150px;margin-left:20px;" />
一直就想见识一下Amazon EC2，因为可以免费使用一年，但由于我一直没有 Visa 卡，所以一直等到现在，网上已经有很多关于如何申请Amazon EC2 的帖子，我在这里不多说，不过我要提醒各位的有如下几点：
<!--more-->

1. 免费使用一年是从你的账号注册那天算起，所以如果你想免费使用一年，最好注册一个新账号。
2. 如果你一不小心使用了一个老账号，你无法确认是否可以免费使用，请登陆Amazon->右上角查看 Account Activity, 如果显示

    ![你可以享受免费试用一年的机会](../images/post/eligible.png)
证明你是可以使用的。
3. 我本来是参考了老N的博客[用Amazon EC2搭建免费WordPress博客及SSH](http://neolee.com/web/use-amazon-ec2-for-wordpress-and-ssh/)，但是有一个问题，就是如果不通过Quick Start来选择实例，搜索出来的已经安装好WordPress的实例都不是免费的。基于这一点，你只能通过Quick Start来选一个未安装Wordpress的实例（我用的Ubuntu）,自己进行wordpress下载安装等。
4. 网上大部分的教程都是比较旧的，现在Amazon EC2 免费部分的 storage 已经由以前的10G 增加到了现在的30G，所以现在的部分Windows 2008的实例也是可以选择的，这样就更方便不熟悉linux的用户使用。

##我出现的问题

>Q: sudo apt-get 安装LAMP时候报错，指令使用正确

**A**: 在你第一次使用 ubuntu 的时候， apt-get 比较旧，所以一定要进行 `sudo apt-get upgrade` 和 `sudo apt-get update`

>Q: 不能安装插件，不能下载主题，不能上传图片

**A**: 进入你的WordPress的文件夹（我的是 /var/www/）,更改wp-content的权限777（我老是感觉不安全）

``` 
    chmod 777 wp-content
```

因为用户www-data(ubuntu)需要对wp-content 文件夹有写的操作，或者你直接把wp-content的group和user 改为 www-data:www-data ,

``` 
    chown -R www-data:www-data wp-content
```

>Q: 无法定位 WordPress 内容目录（wp-content）

**A**: 在wp-content结尾加上

``` js
/** Override default file permissions */
if(is_admin()) {
add_filter('filesystem_method', create_function('$a', 'return "direct";' ));
define( 'FS_CHMOD_DIR', 0751 );
}
```
