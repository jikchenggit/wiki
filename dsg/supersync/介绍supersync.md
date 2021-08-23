---
title: 介绍supersync
description: 介绍supersync
published: true
date: 2021-08-23T03:38:54.063Z
tags: 介绍supersync
editor: markdown
dateCreated: 2020-07-29T06:09:46.503Z
---


# 进程架构图
![image2020-3-26_16-4-11.png](/supersync/image2020-3-26_16-4-11.png)
@startuml
left to right direction
frame source{
database source_database 
card xexp
card redo
file archivelog
agent vagentd
agent sender
agent aoxd
agent dbpsd_s
storage cache
}

  

queue trasmation
frame target{
database target_database 
queue trasmation
agent vagentd_targert
agent loader_s
agent loader_r
agent dbpsd_t
folder cache_r
folder cache_s
folder rowmapping
}

source_database-->xexp
xexp-->trasmation
trasmation--->vagentd_targert
vagentd_targert-->cache_s
cache_s-->loader_s 
loader_s-->target_database
loader_s-->rowmapping
dbpsd_t-->loader_s
dbpsd_s-->aoxd
dbpsd_s-->vagentd
dbpsd_s--> sender
dbpsd_s--> xexp
dbpsd_t-->loader_r
dbpsd_t-->vagentd_targert

source_database-->redo
source_database-->archivelog
redo-->aoxd
archivelog-->aoxd
aoxd-->vagentd
vagentd-->cache
cache-->sender
sender-->trasmation
vagentd_targert-->cache_r
cache_r-->loader_r
loader_r-->target_database
loader_r-->rowmapping

@enduml
## 进程功能说明

### 全量使用到的模块
**1. 归档数据库(product database)**
	 可以为Oracle ,MySQL,postgresql,mongodb等一系列关系数据库.
  
**2. xexp 全量导出工具**
		 类似于Oracle 的expdp 工具,可以通过分析底层数据块,然后进行数据全量导出.
   > 说明此功能只能完成全量功能.
   
**3. Transcation XDT format trail**
		  TCP/IP 网络
**4. 目标端的vagentd**
	 负责接收xexp 发送过来的`xdt` trail 文件,并判断事务逻辑,进行条件判断后进行编号.
**5. cache**
	负责存放xexp 发送过来的全量文件.在`$DBPS_HOME/rmp/sync[0-*]` 文件夹下缓存.
**5.loader -s**
   负责向目标数据库进行数据加载,加载接口是通过(OCI,API,ODBJ等接口). 同时保存一份`rowid` 映射关系到`map`文件.
**6.rowid mapping**
	负责保存源端目标端的rowid ,映射管理,以便能够快速的找到哪一条发生了改变,然后进行数据更新.
  映射关系例子如下:
    |      源端       |      目标端      |
| --------------- | ---------------- |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
 ### 增量使用到的模块
 **1. 归档数据库(product database)**
	 可以为Oracle ,MySQL,postgresql,mongodb等一系列关系数据库.
**1. redo(重做日志)**
	 重做日志包含了数据库所有的数据改变信息,该信息是由矢量和向量信息,指向块的变化位置.
**2. archivelog(归档日志)**
 在特定情况下重做日志可以达到指定大小后,例如(1G大小),日志组将会切换到下一组日志,切换后的redo 日志将会备份为归档日志.
 **3. aoxd**
aoxd 是分析redo(重做日志) 和归档日志的中的矢量和向量信息,然后得出那些数据发生了变化,发生了什么变化.把这种增量信息发送给源端vagentd.
 **3. vagentd**
 **4. 目标端的vagentd**
	 负责接收aoxd 发送过来的`xdt` trail 文件,并判断事务逻辑,进行条件判断后进行编号.然后存放在cache 中路径为`$DBPS_HOME/rmp/real[*]`
 **4. sender**
	 负责存放在cache 中路径为`$DBPS_HOME/rmp/real[*]`  中的*.xf1 文件,按照条件判断发送到目标端`vagentd` 
 **5.loader -r**
   负责将`$DBPS_HOME/rmp/real[*]`中的`xdt` 文件,向目标数据库进行数据加载,加载接口是通过(OCI,API,ODBJ等接口). 同时保存一份`rowid` 映射关系到`map`文件.
 **6.rowid mapping**
	负责保存源端目标端的rowid ,映射管理,以便能够快速的找到哪一条发生了改变,然后进行数据更新.
  映射关系例子如下:
  
  |      源端       |      目标端      |
| --------------- | ---------------- |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |
| AAAAAAAAAAAAAAA | BBBBBBBBBBBBBBBB |


### xdt 文件说明
xdt 文件保存以下的文件信息
- 事务顺序信息
- 操作符信息(DML,DDL)
- 更改前后的数据信息(befor,alter)
- 数据的`rowid` 信息
# 数据流向

![image2020-3-26_16-4-49.png](/supersync/image2020-3-26_16-4-49.png)
## 数据流向说明
### 全量阶段
1. 生产数据库进行正常生产.生产的数据存放在数据文件中.

2. 使用`xexp` 工具分析数据文件块通过`TCP/IP`网络导入到`vagentd`
  > 从xexp 分析数据文件后的数据,是放在内存中的,然后通过TCP/IP 网络发送到目标端(vagentd)进行缓存(cache).
  
3. 目标端`vagentd` 程序接收,并判断事务逻辑和顺序后,把trail 文件存放在cache`$DBPS_HOME/rmp/real[*]`文件夹中.

4. 负责向目标数据库进行数据加载,加载接口是通过(OCI,API,ODBJ等接口). 同时保存一份`rowid` 映射关系到`map`文件.

### 增量同步
1. 生产数据进行正常生产,生产的数据变更的信息存放在`redo`和`归档日志(archivelog)`中.`使用oxad 和 aoxd` 两种方式从redo 和归档日志中抽取增量变化的数据,发送给vagentd

2. 源端vagetnd负责接收aoxd 发送过来的`xdt` trail 文件,并判断事务逻辑,进行条件判断后进行编号.然后存放在cache 中路径为`$DBPS_HOME/rmp/real[*]`

3. 负责存放在cache 中路径为`$DBPS_HOME/rmp/real[*]`  中的*.xf1 文件,按照条件判断发送到目标端`vagentd`
4. 目标端接收源端`sender` 发送的trail 文件.

5. 负责将`$DBPS_HOME/rmp/real[*]`中的`xdt` 文件,向目标数据库进行数据加载,加载接口是通过(OCI,API,ODBJ等接口). 同时保存一份`rowid` 映射关系到`map`文件.


# 版本说明
■  软件版本存放位置
IP：192.168.1.186 /home/file/RealSync.debug

■  软件版本的命名规则
6.2.3.* 代表SuperSync普通版本，不支持压缩表复制,也代表支持非ASM架构的Oracle 数据库.；
6.2.3.\*XML\* 代表SuperSyncXML版本，支持XML表复制；
6.2.3.\*DDL\* 代表SuperSyncDDL版本，支持频繁的DDL表复制；
6.2.3.\*ASM\* 代表SuperSync ASM版本，支持频繁的RAC或ASM 架构上的复制
6.2.3.\*qa\* 代表SuperSync 稳定版本，经过多方测试验证.
6.2.3.\*NEWDBCENTER\* 代表SuperSync双中心版本.

6.3.3.* 代表SuperSync压缩表版本，支持压缩表复制；


7* 代表SmartE产品线

9* 代表EtlPlus产品线

■  32位和64位的区分
6.2.3.178-32bit.ASM.REGISTER-Linux-2.6.18-308.el5PAE-oracle.11.2.0.1.0-2020010219.tar.gz

6.2.3.178-32bit.ASM.REGISTER-Linux-2.6.32-358.el6.x86_64-oracle.11.2.0.1.0-2020010414.tar.gz

6.2.3.178-64bit.ASM.REGISTER-Linux-2.6.32-358.el6.x86_64-oracle.11.2.0.1.0-2019120916.tar.gz

32bit/64bit  ---------------数据库的32和64

X86_64        ---------------操作系统的32和64


