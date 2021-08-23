---
title: 配置PXF
description: 配置PXF
published: true
date: 2021-08-23T04:00:13.037Z
tags: 配置pxf
editor: markdown
dateCreated: 2021-01-29T07:59:49.158Z
---

Greenplum数据库部署由一个主节点和多个段主机组成。初始化和配置Greenplum Platform Extension Framework (PXF)时，在每个Greenplum数据库段主机上启动一个PXF JVM进程。
PXF提供Hadoop、Hive、HBase、对象存储、外部SQL数据存储的连接器。您必须配置PXF以支持计划使用的连接器。

# 配置PXF 必须以下条件
1. 按照 [安装Java for PXF](/greenplum/PXF/install_PXF/install_java) 中的说明，在每个Greenplum数据库段主机上安装Java 8或11。	

2. [初始化PXF服务](init_pxf)
3. 如果计划使用hadoop ,hive 或者hbase 的PXF 连接器,请见[配置PXF hadoop 连接器](hadoop_connect)进行配置
4. 如果您计划使用PXF连接器访问Azure、谷歌云存储、Minio或S3对象存储，您必须执行[配置Azure、谷歌云存储、Minio和S3对象存储](cloud_connet)连接器中的配置步骤。
5. 如果计划使用PXF JDBC连接器访问外部SQL数据库，请执行[配置JDBC连接器](jdbc_connect)中描述的配置过程。
6. 如果您计划使用PXF访问网络文件系统，请执行[配置PXF网络文件系统服务器](fs_connect)中的配置步骤。
7. 启动PXF.