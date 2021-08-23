---
title: 扩展greenplum数据库
description: 扩展greenplum数据库
published: true
date: 2021-08-23T03:49:44.937Z
tags: 扩展greenplum数据库
editor: markdown
dateCreated: 2020-08-27T04:03:39.722Z
---

# Header
为了提高性能和存储容量，可以通过向系统中添加主机来扩展Greenplum数据库系统。通常，向Greenplum集群添加节点可以实现性能和存储容量的线性扩展。


数据仓库通常会随着收集其他数据而增长，现有数据的保留期也会增加。有时，需要增加数据库容量，以便将不同的数据仓库合并为单个数据库。可能还需要额外的计算能力(CPU)来适应新添加的分析项目。虽然在最初指定系统时为增长提供能力是明智的，但通常不可能在需要资源之前就对其进行投资。因此，您应该期望定期执行数据库扩展项目.

由于采用Greenplum MPP架构，当您向系统添加资源时，系统的容量和性能与最初使用添加的资源实现的系统是相同的。数据仓库系统需要大量的停机时间来转储和恢复数据，与此不同，Greenplum数据库系统的扩展是一个具有最小停机时间的分阶段过程。在重新分发数据和维护事务一致性的同时，可以继续执行常规和特别工作负载。管理员可以安排分发活动以适应正在进行的操作，并可以根据需要暂停和恢复。可以对表进行排序，以便按优先顺序重新分发数据集，以确保关键工作负载更快地从扩展的容量中获益，或者释放重新分发非常大的表所需的磁盘空间

扩展过程使用标准的Greenplum数据库操作，因此管理员可以很容易地排除故障。段镜像和任何复制机制仍然是活动的，因此容错不会受到影响，灾难恢复措施仍然有效。



- [系统扩展概述](/zh/greenplum/系统管理/管理Greenplum系统/扩展greenplum数据库/系统扩展概述)
{.links-list}
- [规划Greenplum系统扩展](/zh/greenplum/系统管理/管理Greenplum系统/扩展greenplum数据库/规划Greenplum系统扩展)
{.links-list}

- [准备和添加主机](/zh/greenplum/系统管理/管理Greenplum系统/扩展greenplum数据库/准备和添加主机)
{.links-list}

- [初始化新段主机](/zh/greenplum/系统管理/管理Greenplum系统/扩展greenplum数据库/初始化新段主机)
{.links-list}

- [重新分配表](/zh/greenplum/系统管理/管理Greenplum系统/扩展greenplum数据库/重新分配表)
{.links-list}

- [删除扩展模式](/zh/greenplum/系统管理/管理Greenplum系统/扩展greenplum数据库/删除扩展模式)
{.links-list}
