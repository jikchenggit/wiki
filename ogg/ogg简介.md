---
title: Oracle GoldGate 简介
description: 了解Oracle Goldengate 的概念.
published: true
date: 2021-08-23T03:25:38.768Z
tags: 简介, ogg
editor: markdown
dateCreated: 2020-03-19T07:15:15.743Z
---

# Oracle GoldenGate 简介
了解Oracle GoldGate 的概念,以及在那些场景中使用它,并了解ogg 的相关基本术语和关键字.
## 什么是Oracle GoldenGate?
Oracle GoldenGate 是一个用于实时数据集成和复制的综合性软件包.支持高可用性解决方案,实时数据集成,事务更改,数据捕获,数据复制,转换以及分析系统之间的验证.使用Oracle GoldeGate ,可以跨多个系统进行提交事务.Oracle GoldenGate 允许将Oracle 之间的数据,或者异构数据之间进行数据复制.
## 为什么需要Oracle GoldenGate?
企业数据通常分布在跨企业的异构数据库中.要想获取不同数据之间的数据,可以使用Oracle GoldenGate 来进行实时加载,分发和过滤企业的事务.或者在零停机的情况下允许不同数据之间进行迁移.
Oracle GoldenGate 具有以下关键特性:
- 数据复制是实时的,减少了延时.
- 只有提交的事务才会被复制,从而提高了一致性并提高了性能.
- 支持Oracle 数据库不同版本和发行版,还有不同操作系统上运行的各种异构数据库.可以将数据库从Oracle 数据库复制到不同的异构数据库.
- 结构简单,配置方便.
- 底层数据库和基础设施上最小开销实现高性能.

## 什么时候使用Oracle GoldenGate?
ogg 可以满足可能所有的数据复制需求.常见例子如下.

**业务连续性和高可用性**
业务连续性是企业提供其功能和服务的容错级别.为了实现业务连续性,系统规划了设计了多个服务器,多个存储,多个数据中心,以提供足够高的可用性来支持业务的真正连续性.要建立和维护这样的环境,需要在多个服务器和数据中心之间移动数据,使用ogg 可以做到这一点.

假设意嫁总部位于英国伦敦的跨国银行工作.你再一家银行位于印度办加罗尔的分行工作.该银行在位其金融应用程序使用一个特定的账户,该账户在全球所有分支机构中使用.您的经理要求每天将这个账户中的数据和位于英国的中央数据库进行同步,交易量比较大,而且轻微的延时也是对业务产生很大影响.

**初始加载和数据库迁移**

初始加载时从原数据库提取数据记录并将这些记录加载到目标数据库的过程.初始加载是只执行一次的数据迁移过程.OGG 允许在您不停机的情况下执行数据迁移.

**数据的集成**
数据集成设计到组合来自几个不同的数据源的数据,这些数据源使用各种技术存储,并提供数据的统一视图.OGG 提供实时的数据集成.
## OGG 的拓扑解构yu
安装后,可以配置Oracle Glodengate 满足业务需求.
可以配置简单的单项拓扑到更复杂的对等拓扑.无论架构如何,OGG 提供相似之处.从而使其管理更加容易.
![Description of the illustration configurations.png](https://docs.oracle.com/en/middleware/goldengate/core/19.1/understanding/img/configurations.png)

## OGG 的产品家族
OGG 有很多产品
**OGG Veridata** : 比较两组数据识别不同步的数据,并允许修复不同步的数据.
**OGG Monitor**: 是一个实时的,基于web 的监控控制台,他提供了企业中所有OGG 实例及其关联数据库的一目了然的图形视图.
**OGG for Big Data**: 内置支持从OGG trail 记录写入操作数据到各种大数据目标(如HDFS,HBase,Kaf,Flume,JDBC,Cassandra和MongoDB).
**OGG Application Adapters**: 与OGG 产品集成,引入java 的消息服务(JMS)信息或以JMS 消息或文件形式交付信息.
**Oracle GoldenGate for HP NonStop (Guardian)**: 可以再各种异构应用程序和平台提取和赋值选定的数据记录和事务更改,从而在事务级别管理业务数据.
**Oracle GoldenGate Studio**:通过自动处理和列映射,允许拖放自定义映射,从模板生成最佳时间以及包含上下文的相关帮助,设计和部署高容量的实时复制.
