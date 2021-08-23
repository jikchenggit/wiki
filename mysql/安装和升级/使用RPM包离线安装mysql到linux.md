---
title: 使用RPM包离线安装mysql到linux
description: 使用RPM包离线安装mysql到linux
published: true
date: 2021-08-23T03:22:09.737Z
tags: mysql, linux, rpm
editor: markdown
dateCreated: 2020-03-01T11:24:55.105Z
---

# /使用RPM包离线安装mysql到linux

说明：
> 由于企业版只提供二进制和RPM 包方式。。不提供YUM 安装方式。

- 以下是各个RPM 包的含义：

| **Package Name** | **Summary** |
| --- | --- |
| mysql-commercial-**backup** | MySQL Enterprise Backup (added in 8.0.11) |
| mysql-commercial-**client** | MySQL client applications and tools |
| mysql-commercial-**common** | Common files for server and client libraries |
| mysql-commercial-**devel** | Development header files and libraries for MySQL database client applications |
| mysql-commercial-**embedded-compat** | MySQL server as an embedded library with compatibility for applications using version 18 of the library |
| mysql-commercial-**libs** | Shared libraries for MySQL database client applications |
| mysql-commercial-**libs-compat** | Shared compatibility libraries for previous MySQL installations; the version of the libraries matches the version of the libraries installed by default by the distribution you are using |
| mysql-commercial-**minimal-debuginfo** | Debug information for package mysql-commercial-server-minimal; useful when developing applications that use this package or when debugging this package |
| mysql-commercial-**server** | Database server and related tools |
| mysql-commercial-**server-minimal** | Minimal installation of the database server and related tools (added in 8.0.0) |
| mysql-commercial-**test** | Test suite for the MySQL server |
## 安装步骤
1. 下载对应版本的RPM 包进行解压
```sh
[root@mysql3 ~]# unzip V979091-01.zip 
Archive:  V979091-01.zip
 extracting: mysql-commercial-libs-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-embedded-compat-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-test-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-server-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-server-minimal-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-minimal-debuginfo-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-devel-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-client-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-libs-compat-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-common-8.0.12-1.1.el7.x86_64.rpm  
 extracting: mysql-commercial-backup-8.0.12-1.1.el7.x86_64.rpm  
 extracting: README.txt
```
2. 批量自动安装：自动解决依赖包。使用此命令时注意不要和server_mini 混用。
```sh
yum install mysql-commercial-*
```

* 简易服务端
```sh
sudo yum install mysql-community-{server,client,common,libs}-* 
```
* 简易客户端
```sh
yum install mysql-community-{client,common,libs}-* 
```
* 目录分布

| **Files or Resources** | **Location** |
| --- | --- |
| Client programs and scripts | /usr/bin |
| **[mysqld](file:///D:/refman-8.0-en.html-chapter/programs.html#mysqld "4.3.1 mysqld — The MySQL Server")** server | /usr/sbin |
| Configuration file | /etc/my.cnf |
| Data directory | /var/lib/mysql |
| Error log file | For RHEL, Oracle Linux, CentOS or Fedora platforms: /var/log/mysqld.logFor SLES: /var/log/mysql/mysqld.log |
| Value of **[secure\_file\_priv](file:///D:/refman-8.0-en.html-chapter/server-administration.html#sysvar_secure_file_priv)** | /var/lib/mysql-files |
| System V init script | For RHEL, Oracle Linux, CentOS or Fedora platforms: /etc/init.d/mysqldFor SLES: /etc/init.d/mysql |
| Systemd service | For RHEL, Oracle Linux, CentOS or Fedora platforms: mysqldFor SLES: mysql |
| Pid file | /var/run/mysql/mysqld.pid |
| Socket | /var/lib/mysql/mysql.sock |
| Keyring directory | /var/lib/mysql-keyring |
| Unix manual pages | /usr/share/man |
| Include (header) files | /usr/include/mysql |
| Libraries | /usr/lib/mysql |
| Miscellaneous support files (for example, error messages, and character set files) | /usr/share/mysql |
## 5.1. 安装相关组件
列出开启yum 中所有相关的安装包。，包括未enable 中的源
例如主机上要连接低版本客户端。则需要安装低版本客户端如下：
```sh
rpm --oldpackage -ivh mysql-community-libs-5.5.50-2.el6.x86_64.rpm
```
> 安装其他软件：到官网下载对应版本安装。