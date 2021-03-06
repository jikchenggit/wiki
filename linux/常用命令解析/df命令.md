---
title: df命令
description: df命令
published: true
date: 2021-08-23T03:26:35.829Z
tags: 命令, df
editor: markdown
dateCreated: 2020-03-20T09:35:21.717Z
---

# df 命令
有时你需要知道在某个设备上还有多少磁盘空间。df命令可以让你很方便地查看所有已挂载磁盘的使用情况。
```bash
$ df
Filesystem           1K-blocks      Used Available Use% Mounted on
/dev/sda2             18251068   7703964   9605024  45% /
/dev/sda1               101086     18680     77187  20% /boot
tmpfs                   119536         0    119536   0% /dev/shm
/dev/sdb1               127462    113892     13570  90% /media/disk
$
```
df命令会显示每个有数据的已挂载文件系统。如你在前例中看到的，有些已挂载设备仅限系统内部使用。命令输出如下：

- 设备的设备文件位置；

- 能容纳多少个1024字节大小的块；

- 已用了多少个1024字节大小的块；

- 还有多少个1024字节大小的块可用；

- 已用空间所占的比例；

- 设备挂载到了哪个挂载点上。

df命令有一些命令行参数可用，但基本上不会用到。一个常用的参数是-h。它会把输出中的磁盘空间按照用户易读的形式显示，通常用M来替代兆字节，用G替代吉字节。
```bash
df -h
```

## 常用选项
| | |
| --- | --- |
| -a | 列出所有挂载的文件系统|
| -B | 指定大小单位列出:例如:-BM 就以M 单位列出 | 
| --total | 列出所有文件占总空间的占用率和大小 | 
| -h | 以人类可读的格式(1k,234m,2g) | 
| -i | 例如出inode 信息,而不是数据块信息| 
| -l | 仅列出本地文件系统,不包含其他网络系统 | 
| -T | 列出文件系统类型(ext2,ext3,xfs)|

# 使用场景

## 显示所有信息
```bash
 df -BG -i -T
```

