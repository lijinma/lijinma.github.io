---
layout: post
title: "如何生成微信红包金额"
date: 2015-12-19 20:54:06 +0800
comments: true
categories: 
---

## 一些前提解释

我要讨论的红包是：微信拼手气红包。

拼手机红包的一些的限制条件如下：
-  每个红包最小为0.01元，所以每个红包至少要分到0.01元。

输入数据：

1.  红包总金额 amount
2.  红包个数 count
如果 amount / count < 0.01 元，报错：单个红包金额不可低于0.01元，请重新填写金额。

输出数据：

一个数组：数组中包括 count 个红包金额（每个金额都大于等于0.01元，所有红包的金额加起来等于总金额 amount）

## 一个重要的问题

首先我们先确定一个重要的问题：每个红包的金额是先生成好还是在边抽边生成？

为了解决高并发过程中锁的问题，明显先生成每个红包的金额更简单更效率。

<!-- more -->

## 我的思路

我首先想到的思路：

假设 100 块钱，要发10个拼手气红包。

从1—100 随机10个数字。[20, 61, 97, 39 ,32, 48, 10, 20, 48, 78]

### 第一步：获取随机值

```
share = 随机数/随机数加和

sum = 20+61+97+39+32+48+10+20+48+78 = 453

share1 = 20/453 = 0.04415011
...
share10 = 0.17218543

```
### 第二步：计算红包金额：
```
a1 = share1 * 100 = 0.04415011 * 100 = 4.415011
...
a10 = share10 * 100 = 0.17218543 * 100 = 17.218543
```

好像已经差不多答案了。。

### 但是有几个问题需要考虑：

1.  精度为0.01元，金额不能出现 4.415011
2.  如果算出来的红包金额小于0.01元，怎么办？

解决办法：

1.  所有的红包金额需要 floor （舍掉多余的小数位） : 比如 a1 的金额 从 4.415011 -> 4.41，最后一个红包 = 红包总金额 - 已经 floor 的红包的和
2.  不管是否小于0.01，先把每个红包的金额计算出来。发现自己 = 0 ，从下一个红包中拿0.01，发现自己 = - 0.01 ，从下一个红包中拿 0.02， 直到所有的红包都 > 0

### 可能还有的问题：
1.  如何避免出现100块的红包分给11个人，分成了99块+ 0.1 * 10个情况，不知道现在的微信红包是否可能出现这个问题，这个问题李业（我同事）的做法是使用上面的结果做一个正态分布的换算，非常好的想法。

## 测试：

<form>
红包个数：<input type="text" name="count" id="count" required style="height: 20px;margin-top: 10px;padding: 9px 7px;border: 1px solid #b8b8b8;border-bottom: 1px solid #ccc;outline: none;box-shadow: none;"> <br>
总金额(元)：<input type="text" name="amount" id="amount" required style="height: 20px;margin-top: 10px;padding: 9px 7px;border: 1px solid #b8b8b8;border-bottom: 1px solid #ccc;outline: none;box-shadow: none;"><br>
<input type="button" id="submit" value="获取红包金额" style="cursor: pointer;width: 120px;height: 38px;line-height: 38px;padding: 0;border: 0;background-color: #38f;font-size: 16px;color: white;margin-top: 10px">
</form>
<div id='result'></div>
<script>

$('#submit').click(function(){
  function getRandomInt(min, max) {
    return Math.floor(Math.random() * (max - min)) + min;
  }

  function getNextItemKey(currentKey, count) {
    if (currentKey > count) {
      throw 'Error: currentKey > count';
    }
    if (currentKey != count) {
      return currentKey + 1;
    } else {
      return 1;
    }
  }
  
  var count = $('#count').val();
  var amount = $('#amount').val();
  if (!count) {
    alert('红包个数必须填写');
    return false;
  }
  if (!amount) {
    alert('总金额必须填写');
    return false;
  }
  if (amount / count < 0.01) {
    alert('单个红包金额不可低于0.01元，请重新填写金额');
  }
  amount = amount * 100;
  var items = [];
  for (var i = 0; i < count; ++ i) {
    items[i] = getRandomInt(1, 100);
  }
  var itemAmounts = [];
  var sum = items.reduce(function(pv, cv) { return pv + cv; }, 0);
  var currentAmount = 0;
  for (var i = 0; i < count; ++ i) {
    if (i !== count - 1) {
      itemAmounts[i] = Math.floor(items[i] / sum * amount);
      currentAmount += itemAmounts[i];
    } else {
      itemAmounts[i] = amount - currentAmount
    }
  }
  
  for (var i = 0; i < count; ++ i ) {
    if (itemAmounts[i] > 0) {
      continue;
    }
    var nextKey = getNextItemKey(i, count);
    var diff = 1 - itemAmounts[i];
    itemAmounts[i] = 1;
    itemAmounts[nextKey] -= diff;
  }
  
  for (var i = 0; i < count; ++ i ) {
    itemAmounts[i] = itemAmounts[i] / 100;
  }
  
  alert(itemAmounts.join('元   '));
  
});
</script>
