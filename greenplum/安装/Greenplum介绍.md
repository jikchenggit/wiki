---
title: Greenplum介绍
description: Greenplum介绍
published: true
date: 2021-08-23T03:43:13.525Z
tags: greenplum介绍
editor: markdown
dateCreated: 2020-08-16T13:31:39.747Z
---

# 详细介绍greenplum 数据库系统架构描述
greenplum 数据库通过多个服务器或者主机分配负载,然后处理大量的数据.greenplum 中的逻辑数据库是一个由多个postgresql 数据库的数据库组,共同工作完成对外呈现一个数据库.master 是greenplum 是greenplum 数据库系统的入口点.这个节点是数据库交互的入口点.用于用户链接并提交和运行sql 语句. master 协调程序是协调其他数据库实例之间的负载.这些节点称之为段节点.用于处理数据和存储数据.各个段相互通信,主机之间相互免密互联.

![highlevel_arch.jpg](/greenplum/highlevel_arch.jpg)

greenplum database 是一个软件解决方案;硬件和数据库软件没有耦合.greenplum 数据库运行在greenplum 认证的硬件提供上.性能取决于安装的硬件.由于数据库分布在greenplum 数据库系统中的多台机器,所以选择合适的硬件对于数据库实现最佳的性能至关重要.

本章描述了greenplum 数据库系统的主要组成部分,以及每个组成部分硬件考量和概念,
- greenplum master
- 段节点
- 网络连接
系统可能有使用ETL 用于数据加载,二`topic_e5t_whm_kbb` 用于监工作负载和性能. 

# greenplum master
master 是greenplum 数据库系统的入口点. 数据库服务器进程接受客户端链接,且处理用户发出的sql 命令.用户通过master 使用与postgresql 兼容的客户端程序(如psql或ODBC)链接到greenplum 数据库.

master 主要作用是维护系统目录(一组系统表,其中包含greenplum 数据库系统本身的元数据).但是主目录不包含任何用户数据.数据仅驻留在段主机中.主程序验证客户端链接,协调每个段返回的结果,并将最终结果口显示给客户端程序. 

因为master 不包含任何用户数据,所以它只有很少的磁盘负载.master 需要快速,专门的CPU 用于数据加载,链接处理和查询规划,因为转载文件和备份文件通常需要额外的空间,特别是在生产环境中.客户还决定在master 节点上运行ETL 报告工具,这需要更多的磁盘空间和处理能力. 

# master Reduandancy

可以选择部署master 实例的备份或者镜像,如果master 无法运行,备份主机作为热备用.

在standby master 上运行事务日志复制过程,可以确保备用主机保持最新,并在master 和standby 主机之间同步数据. 如果master 服务器发生故障,日志复制过程将会关闭,管理源可以激活standby 服务器,当standby 服务器处于活动状态时,复制的日志将会在standby 重建master 上次提交事务成功的状态 .

由于master 不包含任何用户数据,所以只需要在master 和standby 之间同步系统编目表.当master更新这些表时,变更会自动复制到standby 表,因此总是与主表同步. 
![standby_master.jpg](/greenplum/standby_master.jpg)


# 段节点 
在greenplum 数据库中,数据段 时存储数据的地方,也是查询发生的地方.业务表和业务索引分布和条带化每个greenplum 数据库的可用段中;每个段数据包含数据的不同部分.
段节点 是为段数据提供服务的服务器进程.用户不直接和greenplum 数据库系统中的片段交互,而是通过master 进行交互.

在参考greenplum 数据库硬件配置中,每个段节点的段实例数量是由有效的CPU 或 CPU 核心数量决定.例如,如果你的段节点有两个双核处理器,那么每台主机有可能有两个或者四个pramriy 段.如果段主机有3 个四核处理,每台主机可能有3个,6 个或者12个段,性能测试将帮助确定所选硬件平台的最佳段数.
`gpinitsystem_config `例子: 
```
#### the specified interface addresses).
declare -a DATA_DIRECTORY=(/opt/primary /opt/primary /opt/primary /opt/primary)

```
此例子配置了每个节点都配置了4个primary 段数据.

# 段冗余
当部署greenplum 数据库系统时,可以选择配置镜像段.镜像段允许在primary 段不可用时,故障转移到镜像段 

镜像段必须使用驻留在与其他主段不同的主机上.镜像段可以设置为两种标准模式.或者自定义镜像段的分布.
- 默认配置: 将当前段节点的所有primary 段数据 放在另一台段主机上. 
- 扩散配置: 将当前段节点的所有primary 端数据 进行拆分,分散到其余的主机上. 
扩散配置要求系统中的主机数量超过primary 段的数量. 
> 例如:
	 每个节点上有4个primary 段数据. 则,需要主机数量大于4 . 

以下图为默认配置的示意图,表示了数据是如何跨段分布的. 
![group-mirroring.png](/greenplum/group-mirroring.png)
## 段故障转移和恢复

当在greenplum 数据库启用镜像段功能时,如果primary 段不可用,系统会自动切换到镜像段.如果段实例或者主机宕机,只有当其余活动段上的所有冗余都可用时,greemplum 数据库系统才能继续运行. 

如果master 不能连接到某个段节点时, 他会在greenplum 数据库系统目录中标记为无效.段实例保持无效且无法进行操作,直到管理源将段恢复.管理可以在系统启动和运行时恢复失效的段节点.

如果没有启用镜像段,并且某个段节点或者端数据失效,系统将会自动关闭.在继续操作之前,管理员必须恢复失效的段 

# 例子 段主机硬件堆栈

无论选择哪种硬件平台,greenplum 都会按照下面配置 

段节点执行大多数数据库处理,因此段主机服务器的配置是决定greenplum 数据库的性能的重要因素. greenplum 数据库的性能与段节点中的最慢的段节点一样慢. 因此,务必确保运行greenplum 数据库底层硬件和操作系统在最佳性能水平运行.建议是所有段主机具有相同的硬件资源和配置. 


段主机应该只为greenplum 工作,避免其他服务进行资源争抢 

下图显示了一个greenplum 数据库段主机的硬件堆栈.主机上有效的CPU 数量是决定每个段主机要部署多少个primary 段数据的基础. 这个图展示了两个CPU 的主机.每个CPU 核心有一个primary 段数据(如果使用镜像段,则镜像段也要对应一个CPU). 
![hardware_stack.jpg](/greenplum/hardware_stack.jpg)



# 磁盘分布
每个CPU 通常映射到一个逻辑磁盘.逻辑磁盘由一个I/O 控制器访问磁盘池的主文件系统组成.逻辑磁盘和文件系统由操作系统提供. 大多数操作系统都允许磁盘驱动器安排物理磁盘组 
![disk_raid.jpg](/greenplum/disk_raid.jpg)
根据选择的硬件平台,不同的RAID 配置提供不同的性能和容量级别.greenplum 支持和认证多种硬件平台和操作系统 


# 网络层

当用户链接到数据库并发查询时,每个段上创建进程处理该查询工作.网络层就是为各个段主机进行进程间的通信,以及通信依赖的网络基础.网络建议使用10GE 以太网 

默认情况下,greenplum database 使用UDP 和流量控制通过网络发送消息. 

greenplum 不会对UDP 做额外的数据包验证和检查.所以可靠性相当与TCP ,性能和可伸缩性却超过TCP . 

## 网络冗余

通过网络部署双10GE 以太交换机和到greenplum master 服务器,和段主机服务器的冗余. 

## 网卡配置
一个段主机通常有多个网卡,可以用于支持流量传输.


可以根据接口的数量,可以跨接口数量分布式链接网络流量. 可以通过段主机分配给特定网络接口实现,并确保primary 段在可用接口上数量均匀的分布平衡.

这种配置可以通过对每个网络接口创建单独的主机地址名实现的. 例如. 如果一台机器有四个网络接口,那么就会有对应四个相应的主机地址,每个地址映射一个或者多个primary 段实例. 应该将`/etc/hosts` 文件配置为不仅包含每台主机的主机名,还要包括所有的接口主机(master,standby,段,ETL 主机)地址.

通过以上配置,操作系统自动选择到目的地的最佳路径,greenplum 数据库自动平衡网络,以达到最大并行性.
![multi_nic_arch.jpg](/greenplum/multi_nic_arch.jpg)

## 交换机配置

在greenplum 数据库数组中使用多个10GE 以太网络交换机,在每个交换机之间平均分配子网数量.在这个示例配置中,如果我们有两个交换机,那么每个NICs1 和2 将会使用交换机1 ,而每个主机上NIC 3 和4 将会使用交换机2.

![multi_switch_arch.jpg](/greenplum/multi_switch_arch.jpg)


# ETL 主机
greenplum 通过外部表特性支持快速并行数据加载.通过外部表和greenplum 数据库并行文件服务器(gpfdist) 结合使用,管理员可以实现最大的并行性,并从greenplum 数据库中加载带宽.许多生产系统部署指定了ETL 服务器来加载数据.这些机器运行greenplum 并行文件服务器(gpfdist) ,单步运行greenplum 数据库实例. 

使用gpfdist 文件服务器的一个优点时,当从外部表数据文件读取数据时 可以充分利用greenplum 数据库系统中的所有段. 

gpfdist 程序可以为段实例提供数据,对于带分隔符文本格式的文件,平均速度誉为350MB/s ,对于CSV 格式的文件,平均速度约为200MB/s ,因此,在运行pgfidst 时,可以考虑以下选项,以最大限度利用ETL 系统的网络带宽. 


- 如果ETL 配置了多个网络接口(NIC) ,在ETL 主机上运行gpfdist 实例,然后定义.这链接接入到统一的交换接口. 
![ext_tables_multinic.jpg](/greenplum/ext_tables_multinic.jpg)

- 在ETL 运行多个gpfdist 实例,并在每个实例平均分配外部数据文件.可以极大的提高负载性能. 

![ext_tables.jpg](/greenplum/ext_tables.jpg)



