---
title: userdel 命令
description: 删除用户
published: true
date: 2021-08-23T03:29:26.684Z
tags: 命令, 用户, userdel
editor: markdown
dateCreated: 2020-04-03T04:30:47.762Z
---

# userdel 命令
如果你想从系统中删除用户，userdel可以满足这个需求。默认情况下，userdel命令会只删除/etc/passwd文件中的用户信息，而不会删除系统中属于该账户的任何文件。

如果加上-r参数，userdel会删除用户的HOME目录以及邮件目录。然而，系统上仍可能存有已删除用户的其他文件。这在有些环境中会造成问题。

下面是用userdel命令删除已有用户账户的一个例子
```bash
userdel -r test
# ls -al /home/test
ls: cannot access /home/test: No such file or directory
```
加了-r参数后，用户先前的那个/home/test目录已经不存在了.
**常用选项**
|     选项     |                                         说明                                         |
| ------------ | ------------------------------------------------------------------------------------ |
| -f, --force  | 此选项在用户仍然登录的情况下仍然删除账户.                                                |
| -r, --remove | 用户主目录中的文件将随用户主目录和用户邮箱一起删除。在其它文件系统中的文件必须手动搜索并删除。 |
