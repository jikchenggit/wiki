---
title: useradd 命令
description: 添加新用户
published: true
date: 2021-08-23T03:29:16.456Z
tags: 命令, useradd, 用户
editor: markdown
dateCreated: 2020-04-03T04:25:19.513Z
---

# useradd 命令
用来向Linux系统添加新用户的主要工具是useradd。这个命令简单快捷，可以一次性创建新用户账户及设置用户HOME目录结构。useradd命令使用系统的默认值以及命令行参数来设置用户账户。系统默认值被设置在/etc/default/useradd文件中。可以使用加入了-D选项的useradd命令查看所用Linux系统中的这些默认值
```bash
[root@dbserver ~]# useradd -D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
[root@dbserver ~]# 
```
在创建新用户时，如果你不在命令行中指定具体的值，useradd命令就会使用-D选项所显示的那些默认值。这个例子列出的默认值如下：

- 新用户会被添加到GID为100的公共组；

- 新用户的HOME目录将会位于/home/loginname；

- 新用户账户密码在过期后不会被禁用；

- 新用户账户未被设置过期日期；

- 新用户账户将bash shell作为默认shell；

- 系统会将/etc/skel目录下的内容复制到用户的HOME目录下；

- 系统为该用户账户在mail目录下创建一个用于接收邮件的文件。

useradd命令允许管理员创建一份默认的HOME目录配置，然后把它作为创建新用户HOME目录的模板。这样就能自动在每个新用户的HOME目录里放置默认的系统文件。在Centos Linux系统上，/etc/skel目录有下列文件：
```bash
[root@dbserver ~]# ls -ltra /etc/skel/
总用量 32
-rw-r--r--    1 root root   172 8月  31 2018 .kshrc
drwxr-xr-x    4 root root    39 11月 19 2018 .mozilla
-rw-r--r--    1 root root   231 6月  14 2019 .bashrc
-rw-r--r--    1 root root   193 6月  14 2019 .bash_profile
-rw-r--r--    1 root root    18 6月  14 2019 .bash_logout
drwxr-xr-x.   3 root root    92 10月  4 13:34 .
drwxr-xr-x. 164 root root 12288 3月  27 16:31 ..
[root@dbserver ~]# ^C
[root@dbserver ~]# 
```
**表 7-1　useradd命令行参数**

|         参数         |                                        描述                                         |
| -------------------- | ---------------------------------------------------------------------------------- |
| `-c *comment*`       | 给新用户添加备注                                                                     |
| `-d *home_dir*`      | 为主目录指定一个名字（如果不想用登录名作为主目录名的话）                                  |
| `-e *expire_date*`   | 用YYYY-MM-DD格式指定一个账户过期的日期                                                 |
| `-f *inactive_days*` | 指定这个账户密码过期后多少天这个账户被禁用；`0`表示密码一过期就立即禁用，`1`表示禁用这个功能 |
| `-g *initial_group*` | 指定用户登录组的GID或组名                                                             |
| `-G *group* ...`     | 指定用户除登录组之外所属的一个或多个附加组                                              |
| `-k`                 | 必须和`-m`一起使用，将/etc/skel目录的内容复制到用户的HOME目录                           |
| `-m`                 | 创建用户的HOME目录                                                                   |
| `-M`                 | 不创建用户的HOME目录（当默认设置里要求创建时才使用这个选项）                              |
| `-n`                 | 创建一个与用户登录名同名的新组                                                         |
| `-r`                 | 创建系统账户                                                                         |
| `-p *passwd*`        | 为用户账户指定默认密码                                                                |
| `-s *shell*`         | 指定默认的登录shell                                                                  |
| `-u *uid*`           | 为账户指定唯一的UID                                                                  |

## useradd 更改默认值
**useradd更改默认值的参数**
|          参数          |                   描述                   |
| ---------------------- | --------------------------------------- |
| `-b *default_home*`    | 更改默认的创建用户HOME目录的位置           |
| `-e *expiration_date*` | 更改默认的新账户的过期日期                 |
| `-f *inactive*`        | 更改默认的新用户从密码过期到账户被禁用的天数 |
| `-g *group*`           | 更改默认的组名称或GID                     |
| `-s *shell*`           | 更改默认的登录shell                       |

- 使用命令更改默认值:

```bash
# useradd -D -s /bin/tsch
# useradd -D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/tsch
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
#
```
