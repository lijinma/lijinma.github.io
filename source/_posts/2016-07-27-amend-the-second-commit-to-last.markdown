---
layout: post
title: "如果修改 Git 的倒数第二个提交"
date: 2016-07-27 12:03:14 +0800
comments: true
categories: 
---

我们都知道 Git Commit 的最后一个提交可以修改：

    $ git commit —amend

使用 Gerrit 过程中有这样的需求，如果想修改倒数第二个提交该怎么办？

可以使用 rebase -i 来操作：

1. `git rebase -i HEAD~2`

2. 修改倒数第二个提交为：edit，保存退出

![rebase edit](https://ww1.sinaimg.cn/large/72f96cbagw1f61la3iguoj20kp0eb78v.jpg)

3. rebase 会停在倒数第二个提交，此时你可以修改代码。

4. 修改完，执行 `git commit —amend`（此时此刻，最后一个提交时倒数第二个提交）

5. `git rebase —continue`
