---
title: greenplum的环境变量
description: greenplum的环境变量
published: true
date: 2021-08-23T03:44:10.074Z
tags: greenplum的环境变量
editor: markdown
dateCreated: 2020-08-21T14:40:53.914Z
---

# greenplum 环境变量
在用户启动的shell 配置文件中设置这些文件(~/.bashrc 或~/.bash_profile) 或者 /etc/profile .

# 需要配置环境变量

> 注意: GPHOME,PATH ,LD_LIBRARY_PATH 这些环境变量可以从`greenplum_path.sh` 文件进行设置.


## GPHOME
数据库软件安装位置:

```
GPHOME=/usr/local/greenplum-db
export GPHOME
```

## PATH
PATH 环境变量需要指向$GPHOME/bin 目录位置.
```
PATH=$GPHOME/bin:$PATH
export PATH
```
## LD_LIBRARY_PATH
```
LD_LIBRARY_PATH=$GPHOME/lib
export LD_LIBRARY_PATH
```

## MASTER_DATA_DIRECTORY
指向 由`gpinitsystem` 创建的数据目录位置.


```bash
MASTER_DATA_DIRECTORY=/opt/master/gpseg-1
export MASTER_DATA_DIRECTORY
```

# 可选的环境变量
以下时标准的postgresql 环境变量,在greenplum 中也可以识别.为了方便,可以把这些环境变量加入到配置文件中,这样不必再命令行上数据呢么多选项. 

## PGAPPNAME
应用程序名称,通常应用程序链接到服务器的设置.此变量默认为psql .名称不能超过63 个字符.
## PGDATABASE

连接时要使用的默认数据库的名称.

## PGHOST
greenplum 数据库master 节点主机名.

## PGHOSTADDR
master 节点主机ip 地址,可以和PGHOST 之外设置选项,避免DNS 查找开销.

## PGPASSWORD
服务器要求密码验证时,使用的密码.出于安全原因,不建议使用此环境变量.考虑使用~/.gppass 文件.
## PGPASSFILE
用于查找密码文件的名称.如果没有设置默认为~/.gppass .

## PGOPTIONS
为greenplum master 主服务器配置的参数..

## PGPORT
主机上greenplum 数据库服务器的端口号,默认为5432.

## PGUSER

用于链接greenplum 的数据库用户名.

## PGDATESTYLE
设置会话日期/时间的默认表示方式.

## PGTZ
设置会话的默认时区(等于 SET timezone TO...)
## PGCLIENTENCODING

设置会话的默认字符集编码(等于 SET client_encoding TO...)







