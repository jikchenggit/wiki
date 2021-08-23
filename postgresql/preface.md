---
title: Untitled Page
description: 
published: true
date: 2021-08-23T03:51:08.421Z
tags: 
editor: markdown
dateCreated: 2020-10-26T14:34:51.939Z
---

# 前言

**目录**

[1. 何为PostgreSQL？](intro-whatis)

[2. PostgreSQL简史](history)

[2.1. 伯克利的POSTGRES项目](history#HISTORY-BERKELEY)

[2.2. Postgres95](history#HISTORY-POSTGRES95)

[2.3. PostgreSQL](history#id-1.3.5.6)

[3. 约定](notation)

[4. 进一步的信息](resources)

[5. 缺陷报告指南](bug-reporting)

[5.1. 标识缺陷](bug-reporting#id-1.3.8.5)

[5.2. 报告什么](bug-reporting#id-1.3.8.6)

[5.3. 向哪里报告缺陷](bug-reporting#id-1.3.8.7)

本书是PostgreSQL的官方文档。 它是PostgreSQL开发人员和其它志愿者并行编写到PostgreSQL的开发中的。它描述了当前版本的PostgreSQL官方支持的所有功能。

为了能够管理有关PostgreSQL的大量信息，本书被组织成了几个部分。每个部分都是针对不同层次的用户，或者说针对具有不同阶段PostgreSQL经验的用户：

- [第 I 部分](tutorial "部分 I. 教程")是一个给新用户的非正式介绍。
    
- [第 II 部分](sql "部分 II. SQL 语言")记载了SQL查询语言环境， 包括数据类型和函数，以及用户级别的性能调优。每个 PostgreSQL用户都应该阅读这些内容。
    
- [第 III 部分](admin "部分 III. 服务器管理")描述服务器的安装和管理。每个运行PostgreSQL服务器的人，不管是个人使用还是为别人维护，都应该阅读这部分内容。
    
- [第 IV 部分](client-interfaces "部分 IV. 客户端接口")描述PostgreSQL客户端程序的编程接口。
    
- [第 V 部分](server-programming "部分 V. 服务器编程")包含为高级用户准备的信息， 比如服务器的扩展能力等。其中的内容包括用户定义数据类型和函数等。
    
- [第 VI 部分](reference "部分 VI. 参考")包含有关 SQL 命令、客户端和服务器程序的参考信息。这部分以按照命令或程序排序的结构化信息支持其它部分。
    
- [第 VII 部分](internals "部分 VII. 内部")包含可用于PostgreSQL开发人员的各类信息。
