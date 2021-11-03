---
title: 安装MySQL
description: 安装MySQL
published: true
date: 2021-11-03T07:15:03.877Z
tags: mysql install
editor: markdown
dateCreated: 2021-11-03T07:15:03.877Z
---


# 安装和升级MySQL
## 本地yum 安装
```bash
yum install -y unzip tar ncurses-compat-libs
```
### 解压软件
```bash
tar -zxvf V1006145-01.zip -C /opt/repo
```

### 关闭Linux 系统自带的MySQL
```bash
vi CentOS-Linux-AppStream.repo
```

### 配置本地yum 源

```bash
cat > /etc/yum.repo.d/mysql.repo << EOF
[mysql-8.0]
name=mysql-8.0
baseurl=file:///opt/repo/mysql-8.0/
gpgkey=file:///opt/repo/RPM-GPG-KEY-mysql
gpgcheck=1
enabled=1
EOF
```
### 开始安装

```sql
yum makecache;
yum install mysql-commercial-server
```
###  启动数据库
```bash
systemctl  start  mysqld
systemctl  status mysqld
```
### 查找密码
```
grep temp /var/log/mysqld.log 
```
### 安装后的操作
#更改密码
```sql
alter user root@'localhost' identified by 'Xj3345091@';
# 密码有复杂度要求，必须有大写、小写、数字、特殊字符，且长度为8 .
```
## 通用二进制安装
### 创建本地用户
```sql
useradd mysql
```
### 本地yum 安装
```bash
yum install -y unzip tar
```
### 创建目录
```bash
mkdir -p /opt/data
mkdir -p /opt/setup
mkdir -p /opt/logs
```
### 解压软件
```bash
tar -xvf mysql-commercial-8.0.23-linux-glibc2.12-x86_64.tar.xz -C /opt/ 
ln -s mysql-commercial-8.0.23-linux-glibc2.12-x86_64/ mysql
```
### 设置环境变量

```bash
export PATH=$PATH:/opt/mysql/bin
```
```bash
mysqld --initialize --user=mysql --basedir=/opt/mysql --datadir=/opt/data
# 注意关注一下密码
```

### 设置启动项
```sql
cp mysql.server  /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
chkconfig --add mysqld
chkconfig mysqld on
systemctl  daemon-reload 
systemctl enable mysqld
```
### 创建my.cnf
```bash
[mysqld]
basedir=/opt/mysql
datadir=/opt/data
log_error=/opt/logs/mysqld_error.log
pid-file=/opt/data/mysqld.pid
socket=/opt/data/mysql.sock
[client]
socket=/opt/data/mysql.sock
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
### 安装帮助文档
```sql
cp /opt/mysql/man/man1/* /usr/share/man/man1/
cp /opt/mysql/man/man8/* /usr/share/man/man8/
```
### 加载时区表

```bash
mysql_tzinfo_to_sql /usr/share/zoneinfo/ | mysql -u root mysql -p
```

