---
title: killall命令
description: kilall 命令
published: true
date: 2021-08-23T03:26:19.345Z
tags: 命令, killall
editor: markdown
dateCreated: 2020-03-20T06:56:48.765Z
---

# killall 命令
```bash
killall http*
```

> 警告　以root用户身份登录系统时，使用killall命令要特别小心，因为很容易就会误用通配符而结束了重要的系统进程。这可能会破坏文件系统。

 -o, 只杀死比较老的进程(在指定实践之前启动).实践为一个浮点数,然后是单位.秒的单位为s,m,h,d,w,m,y, 分,小时,天,星期,月和年分别.
  -y, 只杀死比较老的进程(在指定实践之前启动).实践为一个浮点数,然后是单位.秒的单位为s,m,h,d,w,m,y, 分,小时,天,星期,月和年分别.
  -u,杀死用户下的所有程序.
  -g,杀死所有用户组的程序.