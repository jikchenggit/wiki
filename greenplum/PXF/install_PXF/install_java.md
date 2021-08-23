---
title: 安装JAVA
description: 安装JAVA
published: true
date: 2021-08-23T03:59:48.859Z
tags: 安装java
editor: markdown
dateCreated: 2021-01-29T06:44:49.307Z
---

# 为PXF 安装JAVA
PXF是一个Java服务。它需要在每个Greenplum数据库主机上安装Java 8或Java 11。
# 前提条件
确保可以通过超级用户登录到每台数据库集群主机上.
# 操作过程
以下操作在greenplum 集群中的主,备,和每个段主机上安装JAVA .可以实用`gpssh` 实用程序一次性运行多个命令.
1. 下载java 
java 8: 下载地址

[jdk-8u281-linux-x64.tar.gz](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
java 11: 下载地址
[jdk-11.0.10_linux-x64_bin.tar.gz](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
1. 登录master 主机
```
$ ssh gpadmin@<gpmaster>
```

2. 确定系统上安装的JAVA 版本:
```
gpadmin@gpmaster$ rpm -qa | grep java
```
3. 如果系统没有安装JAVA version 8 或11,则在master 主机上,slave 和段主机上安装JAVA.
   a. 创建一个文本文件,列出greenplum 数据库所有主机(包括master 和slave 和段主机) ,,每行一个主机名称.
```
gpmaster
mstandby
seghost1
seghost2
seghost3
```
b. 上传所下载的JAVA 版本软件
c. 分发到greenplu 所有主机(包括master 和slave 和段主机)
```
gpscp -f host_file jdk-8u281-linux-x64.tar.gz =:/tmp
```
d. 在每台主机上安装java包.例如,要安装javaversion8;
```
gpssh -f gphostfile -e "tar -zxvf jdk-8u281-linux-x64.tar.gz -C /opt/"

```
4. 配置环境变量
如果安装java8 :
```
gpssh -f ghostfile -e "echo "export JAVA_HOME=/opt/jdk1.8.0_241/jre" >> ~/.bash_profile"
````
到此JAVA 环境安装完毕.