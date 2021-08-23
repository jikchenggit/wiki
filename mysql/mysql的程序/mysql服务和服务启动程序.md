---
title: mysql服务和服务启动程序
description: mysql服务和服务启动程序
published: true
date: 2021-08-23T03:24:56.529Z
tags: mysql, 程序
editor: markdown
dateCreated: 2020-03-17T13:49:03.256Z
---

# 1. 服务端程序
## 1.1.  mysqld 程序
mysqld 为服务damon 程序。
- 检查所有MySQL 的程序选项
```sh
mysqld --verbose --help
```

## 1.2. mysqld_safe
mysqld_safe 脚本添加了一些安全措施,例如发生错误时重启服务器,并将信息记录到错误日志中.
mysqld_safe 尝试启动一个名为mysqld 的可执行文件. 使用--mysqld 后者mysqld-version 指定mysqld_safe .
mysqld_safe  和mysqld 的选项大部分都是一致的.
mysqld_safe  从[msyqld] ,[server],[mysql_safe] 配置中读取相关选项.
为了兼容性,mysql_safe 也会读取[safe_mysqld] 配置,但是当前最新版本的MySQL已经改名字为[mysqld_safe]

::: alert-info
对于mysqld_safe 只有通过二进制安装才能有相关脚本。对于RPM 包安装的不需要这个脚本。
:::
## 1.3. mysql.server 
mysql.server  是一个启动mysqld_safe 脚本的启动脚本.适用于 类unix 系统并且是,system v 类型的系统上控制自动启动或关闭的脚本.

```console
mysql.server start
mysql.server stop
```

:::alert-warning
对于某些Linux 平台,使用rpm 包进行MySQL 的安装部署,在这些平台上,mysql.server 和mysqld_safe 将不会被安装,因为MySQL被Systemd 服务控制程序所控制.
:::

- 如果使用Linux rpm 包安装方式,或者本地安装包方式.mysql.server 应该在/etc/init.d ,名字叫mysqld 或者mysql
- 如果使用源码编译安装或者使用二进制部署,则mysql.server 不会自动部署.你必须要手动安装.这个文件会在support-files 文件夹里找到.然后拷贝到/etc/init.d目录下.

```bash
shell> `cp mysql.server /etc/init.d/mysql`
shell> `chmod +x /etc/init.d/mysql`
```
安装完成之后取决你的系统来控制启动和关闭.
Linux:6
```bash
shell> chkconfig --add mysql
shell> chkconfig --level 345 mysql on
```
Linux 7 

```bash
shell> systemctl reload damon
shell> systemctl enable mysql
```
- 在freeBSD 系统上,    要把mysql.server 服务脚本放再/usr'/local/etc/rc.d中用来实现自动启动.
- 或者使用/etc/init.d/boot.local 或/etc/rc.local 文件中添加以下行.
```bash
/bin/sh -c 'cd /usr/local/mysql; ./bin/mysqld_safe --user=mysql &
```
mysql .server 读取选型类型为[mysqld] ,和[mysql.server]

```bash
[mysqld]
datadir=/usr/local/mysql/var
socket=/var/tmp/mysql.sock
port=3306
user=mysql

[mysql.server]
basedir=/usr/local/mysql
```
mysql.server  支持的选项: mysql.server 只支持start,stop 命令行,其他所有选项都要放在配置文件中.
**Table 4.4 mysql.server Option-File Options**

|         Option Name          |                   Description                    |      Type      |
| ---------------------------- | ------------------------------------------------ | -------------- |
| [`basedir`]                  | Path to MySQL installation directory             | directory name |
| [`datadir`]                  | Path to MySQL data directory                     | directory name |
| [`pid-file`]                 | File in which server should write its process ID | file name      |
| [`service-startup-timeout`)] | How long to wait for server startup              | intege         |

## 1.4. mysqld_multi 管理 多个MySQL 实例
msyql_multi 用户管理多个mysqld 进程这些进程侦听不同的unix 套接字tcp/ip 端口的连接.可以启动或者停止服务器,或者报告状态.

:::alert-warning
对于一些linux 平台,从rpm 或者debian 包中安装MySQL 的情形,这种情况都是使用systemd 来管理mysql 服务器的启动和关闭,所以这些情况下默认没有安装mysqld_multi .
:::

mysql_multi 管理多个mysqld 服务器时是使用[mysqldN] 的分类组来管理.N 可以是任何整数.这个数字在MySQL 中称之为组号,或者GNR .区分开.并用作msyqld_multi 的参数,指定哪些服务要启动或者停止或者取得状态.

调用命令格式:
```bash
shell> mysqld_multi [options] {start|stop|reload|report} [GNR[,GNR] ...]
```
范例:
读取mysqld17 中的配置文件参数,启动对应的mysqld 服务.
```bash
shell> mysqld_multi start 17
```
读取mysql8 ,mysql10 到mysql13 的参数,启动范围中的所有mysqld 服务.
```bash
shell> mysqld_multi stop 8,10-13
```

可以查看帮助:
```bash
shell> mysqld_multi --example
```
:::alert-warning
重要说明:
使用mysqld_multi 之前,确保理解传递个mysqld 服务器选项的含义,为什么要单独的mysqld 京成功.使用相同数据目录的多个mysqld 服务器的危险.建议使用独立的数据目录.在线程系统中,使用相同的数据目录启动多个服务器不会带来额外的性能.
:::
:::alert-warning

重要说明2:
建议使用普通用户身份运行mysql ,而不是使用unix 根账户.如果使用根账户运行,MySQL 服务将会能够访问所有其他目录的服务.
:::

- MySQL 配置文件

```
[mysqld_multi]
mysqld     = /usr/local/mysql/bin/mysqld_safe
mysqladmin = /usr/local/mysql/bin/mysqladmin
user       = multi_admin
password   = my_password

[mysqld2]
socket     = /tmp/mysql.sock2
port       = 3307
pid-file   = /usr/local/mysql/data2/hostname.pid2
datadir    = /usr/local/mysql/data2
language   = /usr/local/mysql/share/mysql/english
user       = unix_user1

[mysqld3]
mysqld     = /path/to/mysqld_safe
ledir      = /path/to/mysqld-binary/
mysqladmin = /path/to/mysqladmin
socket     = /tmp/mysql.sock3
port       = 3308
pid-file   = /usr/local/mysql/data3/hostname.pid3
datadir    = /usr/local/mysql/data3
language   = /usr/local/mysql/share/mysql/swedish
user       = unix_user2

[mysqld4]
socket     = /tmp/mysql.sock4
port       = 3309
pid-file   = /usr/local/mysql/data4/hostname.pid4
datadir    = /usr/local/mysql/data4
language   = /usr/local/mysql/share/mysql/estonia
user       = unix_user3

[mysqld6]
socket     = /tmp/mysql.sock6
port       = 3311
pid-file   = /usr/local/mysql/data6/hostname.pid6
datadir    = /usr/local/mysql/data6
language   = /usr/local/mysql/share/mysql/japanese
user       = unix_user4
```
## 1.5. mysql_secure_installation  提高MySQL安装的安全性

* 可以设置root 密码。
* 移除root账号的远程访问权
* 移除匿名账户
* 移除test账户
