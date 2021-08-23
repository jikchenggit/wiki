---
title: 安装greenplum
description: 安装greenplum
published: true
date: 2021-08-23T03:42:02.996Z
tags: 安装greenplum
editor: markdown
dateCreated: 2020-08-08T15:45:56.501Z
---

# 安装数据库
## 下载数据库软件

## 安装greenplum
> 每个节点都要安装


```bash
sudo yum -y install ./greenplum-db-6.9.1-rhel7-x86_64.rpm
```

```bash
sudo chown -R gpadmin:gpadmin /usr/local/greenplum*
sudo chgrp -R gpadmin /usr/local/greenplum*
```

# 配置ssh 免密
## 环境变量
所有节点
```bash
source /usr/local/greenplum-db/greenplum_path.sh
```
## 配置免密
使用ssh-copy-id 命令将公钥添加到集群的其他主机的`authorized_hosts`  文件中
```bash
$ ssh-copy-id smdw
$ ssh-copy-id sdw1
$ ssh-copy-id sdw2
$ ssh-copy-id sdw3
```



## 配置主机间无密码登录
1. 创建` hostfile_exkeys` 文件
```bash
cat >> /home/gpadmin/hostfile_exkeys << EOF
mdw
mdw-1
mdw-2
smdw
smdw-1
smdw-2
sdw1
sdw1-1
sdw1-2
sdw2
sdw2-1
sdw2-2
sdw3
sdw3-1
sdw3-2
```

2. 执行
```bash
gpssh-exkeys -f hostfile_exkeys
```
> 报错
```bash
unable to import module: libssl.so.10: cannot open shared object file: No such file or directory
```
> 不支持centos8 ,请拷贝centos7 以下lib 包.(所有节点和主机)

> 问题解决方案

```bash
scp libssl.so.10 libcrypto.so.10 libnsl.so.1 libreadline.so.6 libtinfo.so.5 192.168.10.240:/lib64
```
### 验证是否成功
1.切换gpadmin
```bash
 su - gpadmin
 ```
 2. 使用以下命令是否成功

```bash
$ gpssh -f hostfile_exkeys -e 'ls -l /usr/local/greenplum-db-<version>'
```
3. 否则重新执行
```bash
 gpssh-exkeys -f hostfile_exkeys
```

