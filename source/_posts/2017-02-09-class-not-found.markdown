---
layout: post
title: "一个小坑提醒：某个 Class 或某个 Trait 突然找不到"
date: 2017-02-09 17:01:51 +0800
comments: true
categories: 
---

“我 Mac 本地没问题啊！”

“但是为什么 Linux 服务器上报这个错啊？？？”
```
[Symfony\Component\Debug\Exception\FatalErrorException] 
Trait "App\Library\XXXX" not found
```
“太诡异了，这怎么查？”

同事中又有人被这个问题坑了，你可以搜到很多人遇到这样的问题，我先告诉大家如何解决，再解释原因。

## 如何解决
1. 查看是否文件名是不是大小写弄错了？
2. 查看 `namespace` 或 `class` 的名字的大小写弄错了？

解决方案就这么简单，肯定是遇到了大小写的问题。

## 为什么 Mac 上没有问题，Linux 上有问题呢？
主要原因是 Mac 上的文件系统 HFS+ 默认是大小写不敏感（case-insensitive）的，当然可以修改为大小写敏感（case-sensitive）。
但是 Linux 系统却默认是大小写敏感的（case-sensitive），所以在 Mac 上能找到的文件，在 Linux 上却找不到。

当然为了了解这个问题，你还需要了解 `PSR-4` 的 autoload 方式。
## 为什么不把 Mac 直接格式化为大小写敏感（case-sensitive）？	
因为历史的原因，如果你格式化为大小写敏感，很多软件都会有问题，比如大名鼎鼎的 Adobe 家的产品就有问题，刚才同事反馈，idea 家的软件也经常会莫名其妙崩溃（最终他不得不重新格式化为不敏感）。
## 在 Mac 上，如果发现文件名大小写有问题，怎么办？
你以为直接把文件名修改就可以解决问题了吗？你太年轻了，文件名大小写变化，git 根本没有任何察觉，还是那个原因，因为文件系统 HFS+ 默认是大小写不敏感的（case-insensitive），（谢谢 @NauxLiu 分享）你可以有这样几个选择：

方案一：
>1. 删除旧文件，提交代码。
>2. 添加新文件，提交代码。

方案二：修改配置
```bash
git config core.ignorecase false
```
方案三：
```bash
$ git mv test.txt tmp.txt
$ git mv tmp.txt Test.txt
$ git commit -m "Renamed test.txt to Test.txt"
```
	
希望对你有帮助。

