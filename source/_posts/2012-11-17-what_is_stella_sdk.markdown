---
layout: post
title: "【用objective-c写android游戏】初次使用Stella SDK"
date: 2012-11-17 23:30
comments: true
categories: 
---

<img src="/images/post/yeecco.png" alt="StellaSDK" target="_blank" style="float:right;width:150px;margin-left:20px;"  />


现在跨平台开发的工具越来越多，比如使用html5的Phone Gap，比如某姓王的提出来的Cocos2d-x等等，还有很多很多，这些平台都可以直接开发ios和android平台的应用，但是对于我却不满意，因为我只会写Objective-c，不会用javascript、c++等，我也不愿意再花大量的时间去学习js、c++、lua等，那怎么办？Stella SDK解决了我的问题。

<!--more-->


##什么是 Stella SDK?

打开Stella SDK的官网：[www.yeecco.com](www.yeecco.com)，我们可以了解到，Stella SDK是一整套开发工具，类似苹果的ios sdk一样，通过它我们可以使用Objective-c来编写的App，并且可以同时生成在ios平台的ipa和android平台的apk。

##为什么可以使用objective-c来写android游戏？

打开产品介绍[http://www.yeecco.com/product/stella](http://www.yeecco.com/product/stella)，我们发现：

>Stella SDK使用纯Objective-C语言在Android NDK上编译而成，如此来避免使用Java。在运行时，无需对代码进一步解析，直接运行在CPU上。正因为多线程框架，Stella SDK充分利用系统资源来保证App在Android设备上性能有最佳表现。

通过介绍，我了解到，他们使用android的NDK来实现的，但就我所知，现在的开发者开发android App的时候，只会用c或c++来开发比较需要高效率的模块，然后使用NDK JNI来集成到android App中，难道整个android app都可以使用NDK来实现？

在进一步学习Stella SDK前，我要简单介绍一下Android NDK。

##什么是 Android NDK?

打开[Android NDK的介绍页面](http://http//developer.android.com/tools/sdk/ndk/overview.html)，我了解到：

>The Android NDK is a toolset that lets you embed components that make use of native code in your Android applications.

>Android applications run in the Dalvik virtual machine. The NDK allows you to implement parts of your applications >using native-code languages such as C and C++. This can provide benefits to certain classes of applications, in the >form of reuse of existing code and in some cases increased speed.

我们一般编写Android App是使用Android SDK，通过java语言来编写程序，而上面提到的是，一些经常被使用的模块，我们可以使用c或c++（objective-c也可以）来编写，这样的好处是可以提交效率。Native code的效率肯定要比Virtual machine快，这是毋庸置疑的。Android NDK的更多信息请大家浏览它的介绍页面。

##Stella SDK的优势是什么？

如果你已经用objective-c开发了几款游戏希望跨平台，如果你只会objective-c且没有更多时间更多精力去学习一门新的语言，比如java、js、lua等，如果你希望只维护一套代码objective-c代码但是想运行在多个平台上？Stella SDK是你最佳的选择，这就是Stella SDK的优势，Stella SDK节省学习新语言的时间，Stella SDK为你节省维护代码的成本。

##Stella SDK是免费获得吗？

登陆[www.yeecco.com官网](www.yeecco.com)，我们免费注册后，就可以直接下载Stlla SDK，我看了一下他们的服务，好像企业定制使用是需要收费的，先下载下来用一下再说，反正是免费的，现在的版本是：StellaSDK_1A3303_samsung.dmg，dmg大小是180多M，看来里面的东西还是不少。

今天就先写到这里，下一篇我把我使用Stella SDK的情况写出来和大家分享，非常好用的一套工具。
