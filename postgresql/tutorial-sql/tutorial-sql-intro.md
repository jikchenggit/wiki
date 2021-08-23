---
title: 引言
description: 引言
published: true
date: 2021-08-23T03:53:02.716Z
tags: 引言
editor: markdown
dateCreated: 2020-10-26T15:05:12.007Z
---

## 2.1. 引言

本章提供一个如何使用SQL执行简单操作的概述。本教程的目的只是给你一个介绍，并非完整的SQL教程。有许多关于SQL的书籍，包括[[melt93\]](biblio.html#MELT93)和[[date97\]](biblio.html#DATE97)。你还要知道有些PostgreSQL语言特性是对标准的扩展。

在随后的例子里，我们假设你已经创建了名为`mydb`的数据库，就象在前面的章里面介绍的一样，并且已经能够启动psql。

本手册的例子也可以在PostgreSQL源代码的目录`src/tutorial/`中找到（二进制PostgreSQL发布中可能没有编译这些文件）。要使用这些文件，首先进入该目录然后运行make：

```
$ cd ..../src/tutorial
$ make
```

这样就创建了那些脚本并编译了包含用户定义函数和类型的 C 文件。接下来，要开始本教程，按照下面说的做：

```
$ cd ..../tutorial
$ psql -s mydb

...


mydb=> \i basics.sql
```

`\i`命令从指定的文件中读取命令。`psql`的`-s`选项把你置于单步模式，它在向服务器发送每个语句之前暂停。 在本节使用的命令都在文件`basics.sql`中。