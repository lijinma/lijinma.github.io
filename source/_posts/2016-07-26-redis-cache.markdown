---
layout: post
title: "你对 Redis 有清晰的定位吗？"
date: 2016-07-26 18:37:08 +0800
comments: true
categories: 
---

最近发现几个同事对 Redis 理解不准确，和他们沟通后，发现他们对缓存和 NoSQL 数据库的概念理解有问题，所以我解释一下我对 Redis 的理解。

1. Redis 可以做 NoSQL 数据库，也可以做缓存。
2. 如果把 Redis 当 NoSQL 用，maxmemory-policy 要设置为 noeviction，因为你当做数据库使用，不能再内存不够的时候把之前的数据扔掉。
3. 如果把 Redis 当缓存使用，maxmemory-policy 要设置为会扔掉数据的选项，比如 volatile-lru （Remove the key with an expire set using an LRU algorithm）
4. 因为两种使用方式下，maxmemory-policy 的不同，Redis 当做 NoSQL 或者缓存，需要分开存放，不能放在一起。
5. 如果 Redis 当缓存在使用，Redis 的实际内存占用可以达到最大内存占用，不用太担心，这和当做 NoSQL 是完全不一样的，NoSQL 的话，需要做好内存的报警和提醒。

就这么多吧。