---
title: 使用yum仓库在线安装mysql到linux
description: 使用yum仓库在线安装mysql到linux
published: true
date: 2021-08-23T03:22:01.945Z
tags: mysql, linux
editor: markdown
dateCreated: 2020-03-01T09:49:58.302Z
---

# 4. YUM 源安装
a.	去下载MySQL 的repo 文件：[http://dev.mysql.com/downloads/repo/yum/](http://dev.mysql.com/downloads/repo/yum/) 
```sh
wget https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
wget https://repo.mysql.com//mysql80-community-release-el8-1.noarch.rpm
```
b.	选择对应操作系统版本的
c.	本地安装

```sh
shell> sudo yum localinstall platform-and-version-specific-package-name.rpm
```

* EL6-based system 
```sh
shell> sudo yum localinstall mysql80013-community-release-el6-{version-number}.noarch.rpm
```
*  EL7-based system:
```sh
shell> sudo yum localinstall mysql80013-community-release-el7-{version-number}.noarch.rpm
```

* Fedora 28:
```sh
shell> sudo dnf localinstall mysql80013-community-release-fc28-{version-number}.noarch.rpm
```
* Fedora 27:
```sh
shell> sudo dnf localinstall mysql80013-community-release-fc27-{version-number}.noarch.rpm
```


* 范例：
```sh
yum localinstall mysql80-community-release-el7-1.noarch.rpm
```

* 查看已经启用MySQL 源
```sh
yum repolist enabled | grep "mysql.*-community.*"
```
* 启用集群安装
```sh
mysql-cluster
```
* 查看所有mysql 有关的安装源
```sh
shell> yum repolist all | grep mysql
```

* 开启对应安装yum源
```sh
shell> sudo yum-config-manager --disable mysql57-community
shell> sudo yum-config-manager --enable mysql80-community
```
* 例如：
开启集群
```sh
shell> sudo yum-config-manager --enable mysql-cluster-7.6-community
```

* 安装MYSQL
```sh
yum install mysql-community-server
```
