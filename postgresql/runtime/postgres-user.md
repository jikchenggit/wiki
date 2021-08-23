---
title: postgresql 的系统用户
description: postgresql 的系统用户
published: true
date: 2021-08-23T03:58:01.030Z
tags: 
editor: markdown
dateCreated: 2020-12-21T06:40:08.165Z
---

## 18.1. PostgreSQL用户账户



和对外部世界可访问的任何服务器守护进程一样，我们也建议在一个独立的用户账户下运行PostgreSQL。这个用户账户应该只拥有被该服务器管理的数据，并且应该不能被其他守护进程共享（例如，使用用户`nobody`是一个坏主意）。我们不建议把可执行文件安装为属于这个用户，因为妥协系统可能接着修改它们自己的二进制文件。

要在你的系统中增加一个 Unix 用户账户，查看一个命令`useradd`或`adduser`。通常会用postgres（本书中也假定用这个账户），但是你可以使用另一个名称。