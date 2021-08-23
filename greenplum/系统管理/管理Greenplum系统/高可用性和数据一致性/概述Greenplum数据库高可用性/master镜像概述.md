---
title: master镜像概述
description: master镜像概述
published: true
date: 2021-08-23T03:47:46.954Z
tags: master镜像概述
editor: markdown
dateCreated: 2020-08-25T14:02:46.512Z
---

# 主镜像概述
您可以在单独的主机上部署master实例的备份或镜像。备份主服务器实例称为standby服务器，在master服务器无法运行时充当热standby服务器。在master服务器联机时，从master服务器创建standby服务器.


当为现有系统启用master 备机时，master主服务器继续向用户提供服务，同时获取master主服务器实例的快照。并部署standby服务器上的快照时，还会记录对master服务器的更改。在standby主服务器上部署快照之后，将同步standby服务器并使用基于Write-Ahead Logging (WAL)的流复制保持当前状态。Greenplum数据库WAL复制使用walsender和walreceiver复制过程.walsender进程是一个主进程。walreceiver是一个备用主进程。

图1所示。在Greenplum数据库中的主镜像

![standby_master.jpg](/greenplum/standby_master.jpg)

由于主服务器不存放用户数据，所以只有系统数据字典在master服务器和standby服务器之间同步。在更新这些表时，捕获更改的复制日志将流到standby主服务器，以使其与master服务器保持同步。在WAL复制期间，所有数据库修改都将在应用之前写入复制日志，以确保任何进程内操作的数据完整性。

这就是Greenplum数据库处理主故障的方式。

如果master服务器失败，Greenplum数据库系统将关闭，master复制过程将停止。管理员运行`gpactivatestandby`实用程序，让standby主服务器接管为新的master服务器。在激活standby主服务器时，复制的日志将重新构造mastrer服务器在上次成功提交事务时的状态。激活的standby主服务器充当Greenplum数据库master服务器，接受初始化standby主服务器时指定的端口上的连接。参见[恢复失败的主服务器]()。

如果在master主服务器处于活动状态时，standby服务器发生故障或变得不可访问，master服务器将跟踪在standby服务器恢复时应用到它的日志中的数据库更改。

这些Greenplum数据库系统目录表包含镜像和复制信息。
- 数据字典表[gp_segment_configuration]()包含主段实例和镜像段实例、主段实例和备用主段实例的当前配置和状态。
-  数据字典表[gp_stat_replication]()包含用于Greenplum数据库主镜像和段镜像的walsender进程的复制统计信息。
