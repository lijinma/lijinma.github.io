---
layout: post
title: "使用 Laravel Queue 不得不明白的知识"
date: 2017-01-31 17:01:51 +0800
comments: true
categories: 
---

![file](https://dn-phphub.qbox.me/uploads/images/201701/31/77/GVKeIASKWW.png)

背景
---
首先说一下我写这篇文章的初衷，在我们打算使用 Laravel Queue 的时候，你的首选应该是去看文档，但是无奈 Laravel 的文档很多地方写得太简单，有时候想了解一个深入的问题，不得不去看源码，但是看源码确实费一些时间。

所以我打算写一篇文章，把我在使用 Laravel Queue 过程中的方方面面都写一下，方便新手学习、老司机温习。

因为 Redis Queue 是比较简单也很常用的一种队列，所以以下内容我都基于 Redis Queue。

为什么使用队列？
---
虽然这个问题不是今天文章的重点，但是我还要说一下，一般来说使用队列是为了：
>1. 异步
>2. 重试

也许你还有其他的理由使用队列，但是这应该是最基本的两个原因。

什么情况使用队列？
---
了解了为什么使用队列，那么一般有这么几类任务使用队列：
>1. 耗时比较久的，比如上传一个文件后进行一些格式的转化等。
>2. 需要保证送达率的，比如发送短信，因为要调用别人的 api，总会有几率失败，那么为了保证送达，重试就必不可少了。

使用队列的时候一定要想明白一个问题，这个任务到底是不是可以异步，如果因为异步会导致问题，那么就要放弃使用队列。

一些小技巧
---
1. 在开发环境我们想测试的时候，可以把 Queue driver 设置成为 `sync`，这样队列就变成了同步执行，方便调试队列里面的任务。
2. Job 里面的 `handle` 方法是可以注入别的 class 的，就像在 Controller action 里面也可以注入一样。

问答
---
### 问：什么时候使用 `queue:listen` 什么时候使用 `queue:work`？

答：Laravel 5.3 的文档已经不写 `queue:listen`这个指令怎么用了，所以你可以看出来可能官方已经不怎么建议使用  `queue:listen`了，但是在本地调试的时候要使用 `queue:listen`，因为 `queue:work`在启动后，代码修改，`queue:work`不会再 Load 上下文，但是 `queue:listen`仍然会重新 Load 新代码。

其余情况全部使用 `queue:work`吧，因为效率更高。

命令讲解
---
以下是常用的指令，我讲解一下
```bash
php artisan queue:work --daemon --quiet --queue=default --delay=3 --sleep=3 --tries=3
```
`--daemon` 
>The queue:work Artisan command includes a --daemon option for forcing the queue worker to continue processing jobs without ever re-booting the framework. This results in a significant reduction of CPU usage when compared to the queue:listen command

总体来说，在 supervisor 中一般要加这个 option，可以节省 CPU 使用。

`--quiet`

不输出任何内容

`--delay=3`

一个任务失败后，延迟`多长时间`后再重试，单位是秒。这个值的设定我个人建议不要太短，因为一个任务失败（比如网络原因），重试时间太短可能会出现连续失败的情况。

`--sleep=3`

去 Redis 中拿任务的时候，发现没有任务，休息`多长时间`，单位是秒。这个值的设定要看你的任务是否紧急，如果是那种非常紧急的任务，不能等待太长时间。

`--tries=3`

定义`失败任务最多重试次数`。这个值的设定根据任务的重要程度来确定，一般 3 次比较适合。

Redis 中发生了什么事情
---
```
dispatch(new ExampleJob());
```
如果一个任务进入 default  队列，会发生：

```
127.0.0.1:6379>monitor

"RPUSH" 
"queues:default"
"{\"job\":\"Illuminate\\\\Queue\\\\CallQueuedHandler@call\",\"data\":{\"commandName\":\"App\\\\Jobs\\\\ExampleJob\",\"command\":\"O:19:\\\"App\\\\Jobs\\\\ExampleJob\\\":7:{s:17:\\\"\\u0000*\\u0000userIdentifier\\\";N;s:9:\\\"\\u0000*\\u0000realIp\\\";N;s:12:\\\"\\u0000*\\u0000requestId\\\";N;s:6:\\\"\\u0000*\\u0000job\\\";N;s:10:\\\"connection\\\";N;s:5:\\\"queue\\\";N;s:5:\\\"delay\\\";N;}\"},\"id\":\"bwA7ICPqnjYiM0ErjRBNwn0kVWF6KeAs\",\"attempts\":1}"
```

redis 中会出现如下内容：
```bash
127.0.0.1:6379> keys queue*
1) "queues:default"
```
如果执行命令:

```bash
php artisan queue:work --daemon --quiet --queue=default --delay=3 --sleep=3 --tries=3
```

Redis 会发生什么事情？

第一步：查看是否需要重启，如果 `laravel:illuminate:queue:restart` 存在，就重启队列（代码更新后，一定要重启队列，否则队列不会读取最新代码）。
```
"GET"
"laravel:illuminate:queue:restart"
```
第二步：查看zset `queues:default:delayed` ，注意这里的事务

```
"WATCH"
"queues:default:delayed"

"ZRANGEBYSCORE"
"queues:default:delayed"
"-inf"
"1485386782"

"UNWATCH"
```

第三步：查看 zset `queues:default:reserved`，注意这里的事务

```
"WATCH"
"queues:default:reserved"

"ZRANGEBYSCORE"
"queues:default:reserved"
"-inf"
"1485386782"

"UNWATCH"
```
第四步：从 `queue:default` list 中取任务，如果有任务，要把任务先暂存到 `queues:default:reserved` 中（过期时间60秒，Redis Queue 里面写一个任务最多执行60秒）。

任务执行结束会把 `queues:default:reserved` 中的任务删除，如果任务报错（Throw exception），也会把`queues:default:reserved` 中的任务删除，然后把任务扔进 `queues:default:delay`，delay 的秒数是 3 秒（因为我们上面参数配置的是 `--delay=3`）。

```
"LPOP"
"queues:default"

#取出任务后，先要放到 queues:default:reserved zset 中

"ZADD"
"queues:default:reserved"
"1485386842" 
"{\"job\":\"Illuminate\\\\Queue\\\\CallQueuedHandler@call\",\"data\":{\"commandName\":\"App\\\\Jobs\\\\ExampleJob\",\"command\":\"O:19:\\\"App\\\\Jobs\\\\ExampleJob\\\":7:{s:17:\\\"\\u0000*\\u0000userIdentifier\\\";N;s:9:\\\"\\u0000*\\u0000realIp\\\";N;s:12:\\\"\\u0000*\\u0000requestId\\\";N;s:6:\\\"\\u0000*\\u0000job\\\";N;s:10:\\\"connection\\\";N;s:5:\\\"queue\\\";N;s:5:\\\"delay\\\";N;}\"},\"id\":\"bwA7ICPqnjYiM0ErjRBNwn0kVWF6KeAs\",\"attempts\":1}"

# 任务执行完毕后， 从 queues:default:reserved zset 中删除

"ZREM"
"queues:default:reserved"
"{\"job\":\"Illuminate\\\\Queue\\\\CallQueuedHandler@call\",\"data\":{\"commandName\":\"App\\\\Jobs\\\\ExampleJob\",\"command\":\"O:19:\\\"App\\\\Jobs\\\\ExampleJob\\\":7:{s:17:\\\"\\u0000*\\u0000userIdentifier\\\";N;s:9:\\\"\\u0000*\\u0000realIp\\\";N;s:12:\\\"\\u0000*\\u0000requestId\\\";N;s:6:\\\"\\u0000*\\u0000job\\\";N;s:10:\\\"connection\\\";N;s:5:\\\"queue\\\";N;s:5:\\\"delay\\\";N;}\"},\"id\":\"bwA7ICPqnjYiM0ErjRBNwn0kVWF6KeAs\",\"attempts\":1}"

# 如果任务失败，会放到 queue:default:delay zset 中

"ZADD"
"queues:default:delayed"
"1485386783" 
"{\"job\":\"Illuminate\\\\Queue\\\\CallQueuedHandler@call\",\"data\":{\"commandName\":\"App\\\\Jobs\\\\ExampleJob\",\"command\":\"O:19:\\\"App\\\\Jobs\\\\ExampleJob\\\":7:{s:17:\\\"\\u0000*\\u0000userIdentifier\\\";N;s:9:\\\"\\u0000*\\u0000realIp\\\";N;s:12:\\\"\\u0000*\\u0000requestId\\\";N;s:6:\\\"\\u0000*\\u0000job\\\";N;s:10:\\\"connection\\\";N;s:5:\\\"queue\\\";N;s:5:\\\"delay\\\";N;}\"},\"id\":\"uuPBCq4QE9ocnw8UbkLhUl2Lh07yPm6M\",\"attempts\":1}"
```
Redis 中的数据结构和操作：
---
### 1. queue:default
数据结构：
`List`，

操作：
`LRANGE "queues:default" 0 -1` 获取 List 里面的所有数据。

### 2. queue:default:reserved 和 queue:default:delay
数据结构：
`Zset`，时间是 zset 的 score，通过 score 来排序。

操作：
`ZRANGE ”queues:default:reserved“ 0 -1` 获取 zset 里面的所有数据。
`ZRANGEBYSCORE queues:default:reserved -inf +inf` 通过时间来排序获取所有数据。

注意
---
Redis 里面一个任务默认最多执行60秒，如果一个任务60秒没有执行完毕，会继续放回到队列中，循环执行，那酸爽（依稀记得那个加班的夜晚........）
![file](https://dn-phphub.qbox.me/uploads/images/201701/31/77/eMY08RMDrp.jpg)



