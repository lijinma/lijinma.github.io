---
layout: post
title: "当 Laravel 5.3 RedisQueue 遇到阿里云 Redis 的时候"
date: 2016-12-23 17:01:51 +0800
comments: true
categories: 
---

公司的微服务的 api 使用的是 Lumen 5.2 来做的，队列使用 Redis 跑得很好。

最近做了一个网站项目，使用了 Laravel 5.3，就在昨天我们使用了 Laravel 5.3 的队列，driver 使用的是 Redis，简单好用。

本地测试顺利，CI 跑完，开开心心的上线。

Bang...... 出错日志疯一样的出现，如下：

```
[2016-12-23 10:07:14] production.ERROR: Predis\Response\ServerException: ERR unknown command eval in /var/www/html/vendor/predis/predis/src/Client.php:370
Stack trace:
#0 /var/www/html/vendor/predis/predis/src/Client.php(335): Predis\Client->onErrorResponse(Object(Predis\Command\ServerEval), Object(Predis\Response\Error))
#1 /var/www/html/vendor/predis/predis/src/Client.php(314): Predis\Client->executeCommand(Object(Predis\Command\ServerEval))
#2 /var/www/html/vendor/laravel/framework/src/Illuminate/Queue/RedisQueue.php(187): Predis\Client->__call('eval', Array)
#3 /var/www/html/vendor/laravel/framework/src/Illuminate/Queue/RedisQueue.php(132): Illuminate\Queue\RedisQueue->migrateExpiredJobs('queues:default:...', 'queues:default')
#4 /var/www/html/vendor/laravel/framework/src/Illuminate/Queue/Worker.php(173): Illuminate\Queue\RedisQueue->pop('queues:default')
```

看一下 RedisQueue 代码就会发现，现在的实现已经大部分都使用 LuaScript 来进行操作，不使用原生方法来操作，使用 LuaScript 来操作好处很多，比如：

* 高效性：减少网络开销及时延，多次redis服务器网络请求的操作，使用LUA脚本可以用一个请求完成
* 数据可靠性：Redis会将整个脚本作为一个整体执行，中间不会被其他命令插入。
* 复用性：LUA脚本执行后会永久存储在Redis服务器端，其他客户端可以直接复用
* 便捷性：实现程序热更新
* 可嵌入性：可嵌入JAVA，C#等多种编程语言，支持不同操作系统跨平台交互
* 简单强大：小巧轻便，资源占用率低，支持过程化和对象化的编程语言
* 免费开源：遵循MIT Licence协议，可免费商用化

----------
这个时候我傻眼了，如果你是我，这个时候会怎么做？脑海里无数个想法：

1. 是否可以降级 Laravel Queue 的版本？之前的版本是使用原生命令的。
2. 是否可以切换别的 driver，但是成本有点高？
3. 阿里云的 Redis 是否可以支持 lua 脚本？
4. 是不是我眼花了？:no_good: 

很明显我没有眼花，而且如果3可以的话，肯定是最方便快速的方案，所以马上搜索，发现今年7月份阿里刚好推出了支持 lua 脚本的 Redis，只不过需要申请。

https://yq.aliyun.com/articles/57805

好开心，马上提交工单，一会就审核通过就开通了 lua 脚本的功能，完美解决。

温馨提示：
1. 开通支持 lua 脚本会有闪断。
2. 可能会丢数据，请做好 Redis 的备份工作，不过我们没有遇见这个问题。
3. 能开通就开通吧，说不准什么时候就用上了。:rose: :rose: :rose: 
