---
title: alias 命令
description: alias 命令解析
published: true
date: 2021-08-23T03:28:38.035Z
tags: 命令, alias
editor: markdown
dateCreated: 2020-04-02T13:18:15.737Z
---

# alias 命令
alias命令是另一个shell的内建命令。命令别名允许你为常用的命令（及其参数）创建另一个名称，从而将输入量减少到最低。

你所使用的Linux发行版很有可能已经为你设置好了一些常用命令的别名。要查看当前可用的别名，使用alias命令以及选项-p
可以使用alias命令创建属于自己的别名.
```bash
$ alias li='ls -li'
$
$ li
total 36
529581 drwxr-xr-x. 2 Christine Christine 4096 May 19 18:17 Desktop
529585 drwxr-xr-x. 2 Christine Christine 4096 Apr 25 16:59 Documents
529582 drwxr-xr-x. 2 Christine Christine 4096 Apr 25 16:59 Downloads
529586 drwxr-xr-x. 2 Christine Christine 4096 Apr 25 16:59 Music
529587 drwxr-xr-x. 2 Christine Christine 4096 Apr 25 16:59 Pictures
529584 drwxr-xr-x. 2 Christine Christine 4096 Apr 25 16:59 Public
529583 drwxr-xr-x. 2 Christine Christine 4096 Apr 25 16:59 Templates
532891 -rwxrw-r--. 1 Christine Christine   36 May 30 07:21 test.sh
529588 drwxr-xr-x. 2 Christine Christine 4096 Apr 25 16:59 Videos
$
```

不过好在有办法能够让别名在不同的子shell中都奏效。下一章中就会讲到具体的做法，另外还会介绍环境变量。