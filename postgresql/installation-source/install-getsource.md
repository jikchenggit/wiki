---
title: 获取源码
description: 获取源码
published: true
date: 2021-08-23T03:57:16.566Z
tags: 
editor: markdown
dateCreated: 2020-12-20T05:53:37.844Z
---

## 16.3. 获取源码

PostgreSQL 12.2 源代码可以从我们的官方网站 https://www.postgresql.org/download/的下载区中获得。你将得到一个名为`postgresql-12.2.tar.gz`或`postgresql-12.2.tar.bz2`的文件。在你获取文件之后，解压缩它：

```
gunzip postgresql-12.2.tar.gz
tar xf postgresql-12.2.tar
```

（如果你得到的是`.bz2`文件，请用`bunzip2`代替`gunzip`）。这样将在当前目录创建一个目录`postgresql-12.2`， 里面是PostgreSQL源代码。 进入这个目录完成安装过程的其他步骤。

你也可以直接从版本控制库中获得源代码，参见[附录 I](sourcerepo)。