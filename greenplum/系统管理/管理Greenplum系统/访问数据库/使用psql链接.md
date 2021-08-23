---
title: 使用psql链接
description: 使用psql链接
published: true
date: 2021-08-23T03:46:27.993Z
tags: 使用psql链接
editor: markdown
dateCreated: 2020-08-23T14:48:09.200Z
---

	
根据使用的默认值或您设置的环境变量，下面的例子展示了如何通过psql访问数据库:
```
$ psql -d gpdatabase -h master_host -p 5432 -U gpadmin
```
```
psql gpdatabase
```
```
$ psql
```

如果还没有创建用户定义的数据库，则可以通过连接到postgres数据库来访问系统。例如:

```
$ psql postgres
```

在连接到数据库之后，psql会提供一个提示，提示psql当前连接到的数据库的名称，后面是字符串=>(如果您是数据库超级用户，则为=#)。例如:
```
gpdatabase=>
```
在提示符下，您可以输入SQL命令。SQL命令必须以A结尾;(分号)以便被发送到服务器并执行。例如:

```
=> SELECT * FROM mytable;
```
有关使用psql客户端应用程序和SQL命令和语法的信息，请参阅[Greenplum参考指南]()。