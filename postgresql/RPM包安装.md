---
title: RPM包安装
description: RPM包安装
published: true
date: 2021-08-23T03:43:02.759Z
tags: rpm包安装
editor: markdown
dateCreated: 2020-08-09T08:13:48.741Z
---

# 1. 安装postgresql 13 到 centos 8

## 在线安装

centos 8.0 在线安装 说明地址
[https://www.postgresql.org/download/linux/redhat/](https://www.postgresql.org/download/linux/redhat/)
![](_v_images/20201014194142614_4457.png =604x)

```
dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
dnf -qy module disable postgresql
dnf install -y postgresql13-server
/usr/pgsql-13/bin/postgresql-13-setup initdb
systemctl enable postgresql-13
systemctl start postgresql-13
```

## 离线安装

### 下载地址:
[https://yum.postgresql.org/rpmchart/](https://yum.postgresql.org/rpmchart/)

### 下载RPM 包
选择必须安装的postgresql12 软件,进行下载. 

[postgresql13-libs-13.0-1PGDG.rhel8.x86_64.rpm](https://download.postgresql.org/pub/repos/yum/13/redhat/rhel-8-x86_64/postgresql13-libs-13.0-1PGDG.rhel8.x86_64.rpm)

[h/postgresql13-13.0-1PGDG.rhel8.x86_64.rpm](https://download.postgresql.org/pub/repos/yum/13/redhat/rhel-8-x86_64/postgresql13-13.0-1PGDG.rhel8.x86_64.rpm)

[postgresql13-server-13.0-1PGDG.rhel8.x86_64.rpm](https://download.postgresql.org/pub/repos/yum/13/redhat/rhel-8-x86_64/postgresql13-server-13.0-1PGDG.rhel8.x86_64.rpm)

### 切换到root 用户进行安装
```bash
dnf install *.rpm
```
# 操作系统配置
## 配置sudo 权限(可选)
```
usermod -aG wheel postgres
passwd postgres
```

## 关闭防火墙
```
systemctl  stop firewalld
systemctl disable firewalld
```
#  数据库配置
## 更改postgersql 密码
```sql
alter user postgres with password '123';
```

# 配置git(可选)
```bash
yum install git
```
# 安装vim
```bash
yum install vim
```

## 设置TCP/IP 连接方式

注意IPv4 的 条目: 允许`192.168.10.X` 网段的主机连接.
```
vi $PGDATA/pg_hba.conf
# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             192.168.10.0/24         scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            scram-sha-256
host    replication     all             ::1/128                 scram-sha-256
```


## 环境变量设置

```bash
cat >> ~/.bash_profile << EOF
export PATH=/usr/pgsql-13/bin:$PATH
# 可选,远程测试使用
export PGHOST=192.168.10.113
export PGPORT=5432
export PGUSER=postgres
EOF
source ~/.bash_profile
```


## 设置监听端口
```sql
alter system set  listen_addresses = '*' ;
```
## 重启数据库
```
pg_ctl restart -D $PGDATA
```

##  配置管理脚本
- 下载管理脚本
- 上传管理脚本到 `PGDATA`中.




