---
title: 使用通用二进制文件在unixlinux安装MySQL
description: 使用通用二进制文件在unixlinux安装MySQL
published: true
date: 2021-10-30T14:30:03.160Z
tags: mysql, 二进制安装
editor: markdown
dateCreated: 2020-03-01T09:42:10.003Z
---

# 使用二进制包部署MySQL 到unix/linux

Oracle 提供了一组MySQL 二进制发行版.使用(扩展名.tar.gz) 提供某些平台的通用发行版.

> Note:
使用二进制安装这种模式不同于yum 安装自动分析相关依赖包。使用这种方式安装,必须首先移除mariadb或者以前的安装过mysql的配置文件。例如/etc/my.conf.且安装相关依赖包.

| 目录| 包含的内容 | 
| --- | --- |
| bin | 包含mysqld,server,客户端,和使用的工具|
| docs | MySQL 手册 (info格式)|
| include | 函数头文件|
| lib | 库文件| 
| share | 错误消息,数据字典,和安装相关的SQL | 
| support-files | 其他的支持文件 | 

## 安装前准备
* 卸载mariadb
```sh
shell> yum remove mariadb*
```
* 安装依赖包
```sh
shell> yum search libaio  # search for info
shell> yum install libaio # install library
```

##  安装MySQL
###  创建MySQL用户和组
```sh
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
```
### 解压软件到指定目录

```sh
shell> cd /usr/local
shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz

shell> gunzip < /path/to/mysql-VERSION-OS.tar.gz | tar xvf -
```
###  创建新的链接文件到真实目录

```sh
shell> ln -s full-path-to-mysql-VERSION-OS mysql
```
> 为了便于版本升级,创建的软连接.

###  设置环境变量
```sh
shell> export PATH=$PATH:/usr/local/mysql/bin
```
###  配置启动目录
```sh
shell> cd mysql
shell> mkdir mysql-files
shell> chown mysql:mysql mysql-files
shell> chmod 750 mysql-files
```
###  初始化数据库
```sh
shell> bin/mysqld --initialize --user=mysql
```

> 初始化数据库两种方式 请选择一种
```sh
shell> bin/mysqld --initialize --user=mysql
shell> bin/mysqld --initialize-insecure --user=mysql
```
{.is-warning}


> NOTE:
> 两种初始化方式: 
第一种必须强制使用密码登录.
第二种方式可以使用跳过密码.
`
shell> mysql -u root --skip-password
`
然后更改对应密码
`
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
`
>1. 在mysql 8.0 默认加密的密码方式已经改了。现在默认使用caching_sha2_password 加密方式。
>2. 如果还需使用默认的mysql_native_password加密方式的话使用默认模式。请使用第二种方式.
>3. 初始化的时候，默认路径为`/usr/local/mysql`

#### 3.2.6.2. 更改默认路径
* 如果要改变安装目录使用以下命令行：
```sh
shell> bin/mysqld --initialize --user=mysql
         --basedir=/opt/mysql/mysql
         --datadir=/opt/mysql/mysql/data
```
* 配置文件更改
```sh
[mysqld]
basedir=/opt/mysql/mysql
datadir=/opt/mysql/mysql/data
```


* 范例
```sh
shell> bin/mysqld --initialize-insecure --user=mysql \
--basedir=/app/mysql \
         --datadir=/app/mysql/mysql-files
```
* 输出日志展示：
```sh
[root@mysql1 mysql]# bin/mysqld --initialize-insecure --user=mysql \
> --basedir=/app/mysql \
>          --datadir=/app/mysql/mysql-files
2018-09-28T16:18:32.178288Z 0 [System] [MY-013169] [Server] /app/mysql-commercial-8.0.12-el7-x86_64/bin/mysqld (mysqld 8.0.12-commercial) initializing of server in progress as process 5328
2018-09-28T16:18:46.268910Z 5 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2018-09-28T16:18:57.082553Z 0 [System] [MY-013170] [Server] /app/mysql-commercial-8.0.12-el7-x86_64/bin/mysqld (mysqld 8.0.12-commercial) initializing of server has completed
```

###  创建安全连接
####  参数说明

| 格式参数 | 描述 |
| --- | --- |
| [--datadir] | Path to data directory |
| [--help] | Display help message and exit |
| [--suffix] | Suffix for X509 certificate Common Name attribute |
| [--uid] | Name of effective user to use for file permissions |
| [--verbose] | Verbose mode |
| [--version] | Display version information and exit |
#### 默认目录创建
```sh
shell> bin/mysql_ssl_rsa_setup
```
#### 指定目录创建
```sh
bin/mysql_ssl_rsa_setup --datadir=/app/mysql/mysql-files/
```

### 设置启动项
```sql
cp mysql.server  /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
chkconfig --add mysqld
chkconfig mysqld on
```
### 创建my.cnf
```bash
[mysqld]
basedir=/opt/mysql
datadir=/opt/data
log_error=/opt/logs/mysqld_error.log
pid-file=/opt/data/mysqld.pid
```
###  创建文件
```bash
touch /opt/logs/mysqld_error.log
chown mysql:mysql /opt/logs/mysqld_error.log
```
###  启动数据库
```bash
systemctl start  mysqld
```