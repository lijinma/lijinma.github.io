---
layout: post
title: "一个年轻程序员对版本管理的理解"
date: 2013-04-09 01:03
comments: true
categories: 
---

![版本管理](/images/version_control.png)

版本管理是每一个项目中必不可少的环节，但这些知识却无法在学校中学到，常常需要在实践中学习，我总结了一些自己工作中版本管理的知识和经验，希望能给大家带来一起启发。
本文章是有感于某外包公司因为人少竟然不用版本管理而发。部分内容参考网上资料。

##为什么做版本管理
如果你不做版本管理：

>1. 你有没有出现过误删程序代码？
>2. 你有没有出现过程序刚刚还运行好好的，现在就不行了，但是忘记修改了什么？
>3. 你是不是在某个时候也很想知道某个文件在某个时间段做过哪些修改？
>4. 你有没有想过和别人合作？

如果以上的答案是肯定的，那么你需要版本管理。

注：即便是单人进行项目开发，比如某些自由职业者，使用版本管理也是非常有用的，没有版本管理你永远无法对项目有一个清晰的认识。

<!--more-->

##Git还是SVN?

现在比较常用的版本管理软件有git和svn，两者的区别我列一下（我以git作为标尺）：

**Git优点：**

    1. Git比SVN能更好的Merge，有木有？有木有？我要对SVN的Merge咆哮了！！
    2. Git比SVN快，比SVN更灵活。
    3. Git的库比SVN小太多，Mozilla项目小了30倍，Git的branch省很多资源。
    4. Git基于分布式设计，开发人员有代码的完全控制权，Git也完全具有SVN的集中式代码管理的能力。
    5. 基于分布式的设计，Git的每一次提交都是成功的，而且不依赖网络连接。
    6. Github有你需要的大多数开源项目。



**Git缺点：**

    1. SVN允许从服务器中获取某个子目录；Git要求你必须clone整个库。
    2. SVN在windows上支持更好，中国使用windows编程的人更多。
    3. GUI方便SVN支持的更好，有更多的可视化选择。
    4. SVN中顺序的版本号非常人性化，Git中中使用SHA1最为commit唯一标志，复杂。
    5. SVN你可以更多的使用并学习，Git需要更长的时间去学习使用。

**我的建议**

如果是没有接触过任何版本管理的新手，建议你直接学习Git，可能会花一段时间才能完全熟悉Git，但这是值得的，当你熟悉了Git，再使用SVN就很容易上手，况且使用Git的项目会越来越多，越多，多……


##版本管理中的一些些总结

我使用的是Git，我使用的IDE是Xcode；


###1. 尽量使用GUI

GUI能更好更快的以可视化展现你的 log、commit、diff等，如果你想更效率的工作，请使用GUI，比如 Mac上的 git gui和 gitk是两个很好很强大的工具，大大的提升了提交速度，也可以更直观的了解各个 branch的关系。


###2. 尽快提交，经常提交

尽快提交和经常提交有如下好处：

1. 每一个提交都是一个新的版本，你又多了一个回滚的机会。
2. 经常提交可以更好的让与你合作者了解到你的工作进度，也为更少的Merge做准备。
3. 尽快提交让你降低出现代码未提交丢失的可能性。


###3. 只提交可以work的代码

这条经验和第一条“尽快提交，经常提交”一样重要，与别人合作的时候，如果你check out到了他的某一个commit，你不希望在这个点是无法编译成功的。所以尽力让每一次提交都不影响整个项目。

###4. 提交前请检查你的更改

有以下内容需要你去检查：

1. 是否提交了无关的内容？
2. 是否提交的内容是你想要提交的内容？这句话并不矛盾。
3. 提交内容的代码格式是否正确，我想每一个公司都会有自己的代码格式要求。


###5. 认真填写“Commit messages”

你要了解 Commit messages就是你所提交的内容的中心思想，如果你写了一堆废话，不如不写。

如果你不希望每次都查看提交细节，请认真书写你的commit messages，这对以后版本管理有太多好处了，这在团队合作中太重要了。

即便你一个人写你一个人的代码，你也要为你自己的每一次提交负责，因为你不想在以后浪费更多的时间。

###6. 做好你的.gitignore

请ignore掉不需要的文件或者编译生成的一些临时文件，比如mac目录下的.DS_Store，比如Android项目生成的bin中的内容。

###7. 要注意IDE项目文件

两点需要你注意：

1. xcode 的项目我会只提交一个 projectname.xcodeproj/project.pbxproj，其余的文件是一些类似个人配置文件，不需要提交。
2. 请把project.pbxproj的提交与源代码的提交分开，原因是project.pbxproj是无法merge的。



**如果我说的部分有任何问题，请赐教，也希望能和你关于版本管理有一些交流。**



##参考资料：


[5 Fundamental differences between GIT & SVN](http://boxysystems.com/index.php/5-fundamental-differences-between-git-svn/)

[GitSvnComparison](https://git.wiki.kernel.org/index.php/GitSvnComparison)

[10 commandments of good source control](http://www.troyhunt.com/2011/05/10-commandments-of-good-source-control.html)