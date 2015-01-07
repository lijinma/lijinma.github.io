---
layout: post
title: "Linux/Unix 指令使用说明的格式介绍（The bash command 'usage' syntax）"
date: 2014-08-24 20:32:17 +0800
comments: true
categories: 
---

此片文章是对自己的一个提醒！

很多时候在 Linux/Unix 平台上看到一个命令的 Usage 的时候，以为看懂了，其实根本没看懂，还需要通过 man 来查阅，今天我就讲讲 Usage 的语法（syntax）。

    bold text          type exactly as shown.
    italic text        replace with appropriate argument.
    [-abc]             any or all arguments within [ ] are optional.
    -a|-b              options delimited by | cannot be used together.
    argument ...       argument is repeatable.
    [expression] ...   entire expression within [ ] is repeatable.


举例

    $ cp
    usage: cp [-R [-H | -L | -P]] [-fi | -n] [-apvX] source_file target_file
	       cp [-R [-H | -L | -P]] [-fi | -n] [-apvX] source_file ... target_directory

其中`cp [-R [-H | -L | -P]] [-fi | -n] [-apvX] source_file ... target_directory`包括6部分：

`cp` 是指令名称。

`[-R [-H | -L | -P]]` 一个 options 块，`-H`、`-L`和`-P`三个选项互斥。

`[-fi | -n]` 一个 options 块， `-fi`和`-n`选项互斥

`[-apvX]` 一个 options 块， 所有在[]内的参数可以不选择使用，也可以全部选择使用。

`source_file ...` 文件名，`...`表示可以有多个文件，就是可以有多个 `source_file`

`target_directory` 一个参数，表示要复制文件的最终文件夹。

实例：

    $ cp file1 file2 file3 directory
    $ ls direcoty
    file1 file2 file3

妈妈再也不用担心我不懂 Command usage 了。


参考：

http://serverfault.com/questions/124616/how-to-interpret-the-bash-command-usage-syntax
http://pcsupport.about.com/od/commandlinereference/a/command-syntax.htm

