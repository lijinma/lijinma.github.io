---
layout: post
title: "PHP 数组的尴尬"
date: 2016-01-07 11:47:20 +0800
comments: true
categories: 
---

## 问题：
PHP 中，不管是 list 或者 dictionary 都使用一样的 `[]`(或者 `array()`) 来定义。

在使用 JSON 作为 API 数据 Content-Type 的时候，会有这样一个问题：如何返回一个空对象和一个空数组？

使用 `json_encode([])` 得到的结果是 `[]`(json)

那如何返回一个 `{}` (json)

## 方法：

需要使用：`json_encode(new stdClass)`
