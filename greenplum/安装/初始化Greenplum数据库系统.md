---
title: 初始化Greenplum数据库系统
description: 初始化Greenplum数据库系统
published: true
date: 2021-08-23T03:42:12.211Z
tags: 初始化greenplum数据库系统
editor: markdown
dateCreated: 2020-08-08T15:47:29.097Z
---

# 初始化数据库
## 创建system文件
```bash
cat >> hostfile_gpinitsystem << EOF
node1
node2
node3
node4
EOF
```
> 说明: 只包含段数据主机,不包含master 和standby主机.

## 创建配置文件
```bash
cp $GPHOME/docs/cli_help/gpconfigs/gpinitsystem_config  /home/gpadmin/gpconfigs/gpinitsystem_config
```
1.更改如下内容
```
declare -a MIRROR_DATA_DIRECTORY=(/opt/mirror /opt/mirror /opt/mirror /opt/mirror)
declare -a DATA_DIRECTORY=(/opt/primary /opt/primary /opt/primary /opt/primary)
MACHINE_LIST_FILE=/home/gpadmin/gpconfigs/hostfile_gpinitsystem
ENCODING=UNICODE
MASTER_PORT=5432
MASTER_DIRECTORY=/opt/master
MASTER_HOSTNAME=master
PORT_BASE=6000
SEG_PREFIX=gpseg
ARRAY_NAME="Greenplum Data Platform"
```

## 初始化单节点master
```bash
$ cd ~
$ gpinitsystem -c gpconfigs/gpinitsystem_config -h gpconfigs/hostfile_gpinitsystem
```
## 初始化standby 
```bash
 gpinitsystem -c gpconfigs/gpinitsystem_config -h gpconfigs/hostfile_gpinitsystem   -s standby  
```
> /opt/master 目录存在.否则报错.

## 确认配置
```bash
=> Continue with Greenplum creation? Yy/Nn
```

## 设置时区

```bash
createdb testDB
```
psql 可以链接数据了.

# 初始化mirror 段实例
## 生成配置文件
```bash
 gpaddmirrors -o ~/sample_mirror_config
 ```
 ## 创建mirror 段
 ```
 gpaddmirrors -i ./sample_mirror_config
 ```
 生成的配置文件.
 ```
 [gpadmin@gp-master ~]$ cat sample_mirror_config 
0|node2|7000|/opt/mirror/gpseg0
1|node2|7001|/opt/mirror/gpseg1
2|node2|7002|/opt/mirror/gpseg2
3|node2|7003|/opt/mirror/gpseg3
4|node3|7000|/opt/mirror/gpseg4
5|node3|7001|/opt/mirror/gpseg5
6|node3|7002|/opt/mirror/gpseg6
7|node3|7003|/opt/mirror/gpseg7
8|node4|7000|/opt/mirror/gpseg8
9|node4|7001|/opt/mirror/gpseg9
10|node4|7002|/opt/mirror/gpseg10
11|node4|7003|/opt/mirror/gpseg11
12|node1|7000|/opt/mirror/gpseg12
13|node1|7001|/opt/mirror/gpseg13
14|node1|7002|/opt/mirror/gpseg14
15|node1|7003|/opt/mirror/gpseg15
```
 
 ## 检查是否创建成功
 
 ```
 gpstat -s/gpstat-m
 ```
 > streaming 为正在同步, `Synchronized` 同步完成.
## 重新初始化
```bash
gpssh -f /home/gpadmin/hostfile_exkeys -e -v "cd /opt/ && rm -rf master/ && sudo rm -rf primary/ && sudo rm -rf mirror/ "


gpssh -f /home/gpadmin/hostfile_exkeys -e -v "cd /home/gpadmin/ && rm -rf gpAdminLogs/"
```

