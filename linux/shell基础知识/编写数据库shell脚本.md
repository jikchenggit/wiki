---
title: 编写MySQL数据库shell脚本
description: 编写MySQL数据库shell脚本
published: true
date: 2021-08-23T03:39:49.907Z
tags: 编写mysql数据库shell脚本
editor: markdown
dateCreated: 2020-08-01T14:42:10.050Z
---

# 使用shell 进行交互式访问MySQL 数据库
**2. 向服务器发送命令**
在建立起到服务器的连接后，接着就可以向数据库发送命令进行交互。有两种实现方法：

- 发送单个命令并退出；

- 发送多个命令。

要发送单个命令，你必须将命令作为mysql命令行的一部分。对于mysql命令，可以用-e选项。
```bash
$ cat mtest1
#!/bin/bash
# send a command to the MySQL server

MYSQL=$(which mysql)

$MYSQL mytest -u test -e 'select * from employees'
$ ./mtest1
+-------+----------+------------+---------+
| empid | lastname | firstname  | salary  |
+-------+----------+------------+---------+
|     1 | Blum     | Rich       | 25000   |
|     2 | Blum     | Barbara    | 45000   |
|     3 | Blum     | Katie Jane | 34500   |
|     4 | Blum     | Jessica    | 52340   |
+-------+----------+------------+---------+
$
```