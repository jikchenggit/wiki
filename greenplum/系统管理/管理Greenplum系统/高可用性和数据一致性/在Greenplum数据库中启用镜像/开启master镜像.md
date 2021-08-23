---
title: 开启master镜像(standby)
description: 开启master镜像(standby)
published: true
date: 2021-08-23T03:48:08.824Z
tags: 开启master镜像(standby)
editor: markdown
dateCreated: 2020-08-25T14:33:26.151Z
---

您可以使用gpinitsystem配置新的Greenplum数据库系统和备用主服务器，或者稍后使用gpinitstandby启用它。本主题假设您正在向初始化时没有备用主服务器的现有系统添加备用主服务器。

有关实用程序[gpinitsystem]()和[gpinitstandby]()的信息，请参阅[Greenplum数据库实用程序指南]()。

# 将备用主机添加到现有系统

1. 确保安装和配置了standby主机:创建了gpadmin系统用户，安装了Greenplum数据库二进制文件，设置了环境变量，交换了SSH密钥，如果需要，还创建了数据目录和表空间目录。

2. 在当前活动的master上运行gpinitstandby实用程序，将备用主主机添加到Greenplum数据库系统中。例如:
```
$ gpinitstandby -s smdw
```
其中-s指定standby主机名。

若要将操作切换到备用主服务器，请参阅[恢复失败的主服务器]()。

# 检查主镜像进程的状态(可选)
您可以使用-f选项运行gpstate实用程序，以显示备用主主机的详细信息。
```
$ gpstate -f
```

备用主服务器状态应该是被动的，而WAL发送方状态应该是流的。

有关[gpstate]()实用程序的信息，请参阅[Greenplum数据库实用程序指南]()。

