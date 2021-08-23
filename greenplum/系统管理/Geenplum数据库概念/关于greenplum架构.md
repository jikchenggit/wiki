---
title: 关于greenplum架构
description: 关于greenplum架构
published: true
date: 2021-08-23T03:44:39.154Z
tags: 关于greenplum架构
editor: markdown
dateCreated: 2020-08-21T15:19:42.491Z
---

# 架构
Greenplum数据库是一个大规模并行处理(MPP)数据库服务器，它的架构是专门设计用来管理大规模分析数据仓库和商业智能工作负载的。

MPP(也称为无共享架构)是指具有两个或多个处理器协同执行一个操作的系统，每个处理器有自己的内存、操作系统和磁盘。Greenplum使用这种高性能的系统架构来分配数tb数据仓库的负载，并且可以并行地使用系统的所有资源来处理查询。

Greenplum数据库是基于PostgreSQL的开源技术。它本质上是几个面向PostgreSQL磁盘的数据库实例共同作为一个内聚的数据库管理系统(DBMS)。它基于PostgreSQL 9.4，在大多数情况下，在SQL支持、特性、配置选项和最终用户功能方面与PostgreSQL非常相似。数据库用户与Greenplum数据库进行交互，就像他们与常规的PostgreSQL DBMS进行交互一样。

Greenplum数据库可以使用附加优化(AO)存储格式批量加载和读取数据，并提供了优于堆表的性能优势。附加优化存储为数据保护、压缩和行/列方向提供校验和。可以压缩面向行或面向列的、添加优化的表。

Greenplum Database和PostgreSQL之间的主要区别如下:
- 除了Postgres计划器之外，还利用GPORCA进行查询规划。
- Greenplum数据库可以使用附加优化存储
- Greenplum Database可以选择使用列存储，数据在逻辑上组织为一个表，使用物理上以面向列的格式存储的行和列，而不是作为行。列存储只能用于追加优化的表。列存储可压缩。它还可以提供性能改进，因为您只需要返回感兴趣的列。所有压缩算法都可以用于面向行或面向列的表，但运行长度编码(RLE)压缩只能用于面向列的表.Greenplum数据库对所有使用列存储的附加优化表提供压缩。

对PostgreSQL的内部进行了修改或补充，以支持Greenplum数据库的并行结构。例如，系统目录、优化器、查询执行器和事务管理器组件已经被修改和增强，以便能够跨所有并行PostgreSQL数据库实例同时执行查询。Greenplum interconnect(网络层)支持不同PostgreSQL实例之间的通信，并允许系统作为一个逻辑数据库运行。

Greenplum Database还可以使用声明式分区和子分区来隐式地生成分区约束。

Greenplum数据库还包括为优化PostgreSQL的商业智能(BI)工作负载而设计的特性。例如，Greenplum增加了并行数据加载(外部表)、资源管理、查询优化和存储增强，这些都是标准PostgreSQL中没有的。Greenplum开发的许多特性和优化已经进入了PostgreSQL社区。例如，表分区首先是由Greenplum开发的，现在在标准PostgreSQL中。

Greenplum数据库查询使用Volcano-style的查询引擎模型，其中执行引擎采用一个执行计划，并使用它生成物理操作符树，通过物理操作符对表进行评估，并在查询响应中交付结果。

Greenplum数据库通过将数据和处理工作负载分布在多个服务器或主机上来存储和处理大量数据。Greenplum数据库是一组基于PostgreSQL 9.4的独立数据库，它们一起工作来呈现一个单一的数据库图像。master是Greenplum数据库系统的入口点。它是客户端连接并提交SQL语句的数据库实例。主程序与系统中的其他数据库实例协调工作，这些实例称为段，用于存储和处理数据。

Figure 1. 高级的greenplum 数据库架构

![highlevel_arch.jpg](/greenplum/highlevel_arch.jpg)

以下就开始介绍组件和如何协同工作.
# master 节点
Greenplum数据库主服务器是Greenplum数据库系统的入口，它接受客户端连接和SQL查询，并将工作分发给段实例。

Greenplum数据库终端用户(通过master)与Greenplum数据库进行交互，就像他们与典型的PostgreSQL数据库进行交互一样。它们使用客户端程序(如psql)或应用程序编程接口(API)(如JDBC、ODBC或libpq (PostgreSQL C API))连接数据库。

主目录是全局系统目录驻留的位置。全局系统目录是一组系统表，其中包含关于Greenplum数据库系统本身的元数据。主服务器不包含任何用户数据;数据仅驻留在段中。主程序验证客户端连接、处理传入的SQL命令、在段之间分配工作负载、协调每个段返回的结果，并将最终结果显示给客户端程序。

Greenplum数据库使用预写日志(WAL)进行主/备用主镜像。在WAL-based日志记录中，所有修改都在应用之前写入日志，以确保任何进程内操作的数据完整性。

# segments 节点

Greenplum数据库段实例是独立的PostgreSQL数据库，每个实例存储一部分数据并执行大部分查询处理。

当用户通过Greenplum master连接到数据库并发出查询时，将在每个段数据库中创建进程来处理该查询的工作。有关查询过程的更多信息，请参见关于[Greenplum查询处理]()。

在Greenplum数据库系统中，用户定义的表及其索引分布在可用的段中;每个段包含不同的数据部分。为段数据提供服务的数据库服务器进程在相应的段实例下运行。用户通过master与Greenplum数据库系统中的片段进行交互。

segments 段运行在称为段主机的服务器上。段主机通常执行2到8个Greenplum段，具体取决于CPU核心、RAM、存储、网络接口和工作负载。段主机期望被相同地配置。从Greenplum数据库获得最佳性能的关键是将数据和工作负载均匀地分布在大量具有同等能力的段上，以便所有段同时开始处理任务并完成它们的工作。

# 关于greenplum 互联
interconnect是Greenplum数据库体系结构的网络层。


互连指的是段和网络基础设施之间的进程间通信，这种通信依赖于网络基础设施。Greenplum互连使用标准的以太网交换结构。出于性能考虑，建议使用10千兆或更快的系统。

默认情况下，互连使用带有流控制(UDPIFC)的用户数据报协议来通过网络发送消息。Greenplum软件执行的数据包验证超出了UDP提供的范围。这意味着可靠性相当于传输控制协议(TCP)，性能和可伸缩性超过TCP。如果将互连改为TCP, Greenplum数据库的可伸缩性限制为1000个段实例。当UDPIFC作为互连的默认协议时，这个限制就不适用了。
