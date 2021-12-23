---
title: postgresql
description: postgresql doc
published: true
date: 2021-12-23T03:00:15.704Z
tags: doc, postgresql
editor: markdown
dateCreated: 2020-02-29T14:56:25.337Z
---

# PostgreSQL 12.2 速查
[1. 表空间概念](search-tablespaces)
[2. WalMiner 使用手册](WalMiner)
[3. 分析Toast Pointer数据块](Toast_Pointer)

# PostgreSQL 12.2 手册

**摘要**

PostgreSQL 9.6版本以后的中文手册最初基于[彭煜玮](http://www.pengyuwei.net/)副教授翻译的 [《PostgreSQL 9.6.0 文档》](http://www.pengyuwei.net/PGDOC/960/index)，后续版本的中文手册主要在前一版本的基础上作增量翻译。 《PostgreSQL 12.2手册》基于前一版本的[《PostgreSQL 11.2手册》](http://www.postgres.cn/docs/11)翻译。 翻译工作由文档翻译组志愿者chegong18,sizhitu,sunshinerxu,ChenHuajun,jingsam,messon007等完成。 详细请参考[PostgreSQL 12中文手册的翻译](https://github.com/postgres-cn/pgdoc-cn/wiki/pg12)。 如果发现中文手册中存在问题，请向[Github源码仓库](https://github.com/postgres-cn/pgdoc-cn)提交问题报告(Issue)或修订Patch(PR)。

中文手册版本:1.0 最后更新时间:2020-08-18

***
**目录**

# [前言](preface)

[1. 何为PostgreSQL？](intro-whatis)

[2. PostgreSQL简史](history)

[3. 约定](notation)

[4. 进一步的信息](resources)

[5. 缺陷报告指南](bug-reporting)

# [I. 教程](tutorial)

[1. 从头开始](tutorial-start)

[2. SQL语言](tutorial-sql)

[3. 高级特性](tutorial-advanced)




# [II. 服务器管理](admin)

[16. yum 源安装](installation)

[17. 源码安装](installation-source)


[18. 服务器设置和操作](runtime)

[19. 服务器配置](runtime-config)

[20. 客户端认证](client-authentication)

[21. 数据库角色](user-manag)

[22. 管理数据库](managing-databases)

[23. 本地化](charset)

[24. 日常数据库维护工作](maintenance)

[25. 备份和恢复](backup)

[26. 高可用、负载均衡和复制](high-availability)

[27. 监控数据库活动](monitoring)

[28. 监控磁盘使用](diskusage)

[29. 可靠性和预写式日志](wal)

[30. 逻辑复制](logical-replication)

[31. 即时编译（JIT）](jit)

[32. 回归测试](regress)

# [III. 客户端接口](client-interfaces)

[33. libpq - C 库](libpq)

[34\. 大对象](largeobjects)

[35. ECPG - C 中的嵌入式 SQL](ecpg)

[36. 信息模式](information-schema)

# [IV. 服务器编程](server-programming)

[37. 扩展 SQL](extend)

[38. 触发器](triggers)

[39. 事件触发器](event-triggers)

[40. 规则系统](rules)

[41. 过程语言](xplang)

[42. PL/pgSQL - SQL过程语言](plpgsql)

[43. PL/Tcl - Tcl 过程语言](pltcl)

[44. PL/Perl - Perl 过程语言](plperl)

[45. PL/Python - Python 过程语言](plpython)

[46. 服务器编程接口](spi)

[47. 后台工作者进程](bgworker)

[48. 逻辑解码](logicaldecoding)

[49. 复制进度追踪](replication-origins)

# [V. 参考](reference)

[I. SQL 命令](sql-commands)

[II. PostgreSQL 客户端应用](reference-client)

[III. PostgreSQL 服务器应用](reference-server)

# [VI. 内部](internals)

[50. PostgreSQL内部概述](overview)

[51. 系统目录](catalogs)

[52. 前端/后端协议](protocol)

[53. PostgreSQL编码习惯](source)

[54. 本国语言支持](nls)

[55. 编写一个过程语言处理器](plhandler)

[56. 编写一个外部数据包装器](fdwhandler)

[57. 编写一种表采样方法](tablesample-method)

[58. 编写一个自定义扫描提供者](custom-scan)

[59. 遗传查询优化器](geqo)

[60. 表访问方法接口定义](tableam)

[61. 索引访问方法接口定义](indexam)

[62. 通用WAL 记录](generic-wal)

[63. B-树索引](btree)

[64. GiST 索引](gist)

[65. SP-GiST索引](spgist)

[66. GIN 索引](gin)

[67. BRIN 索引](brin)

[68. 数据库物理存储](storage)

[69. 系统目录声明和初始内容](bki)

[70. 规划器如何使用统计信息](planner-stats-details)

# [VII. 附录](appendixes)

[A. PostgreSQL错误代码](errcodes-appendix)

[B. 日期/时间支持](datetime-appendix)

[C. SQL关键词](sql-keywords-appendix)

[D. SQL 符合性](features)

[E. 版本说明](release)

[F. 额外提供的模块](contrib)

[G. 额外提供的程序](contrib-prog)

[H. 外部项目](external-projects)

[I. 源代码仓库](sourcerepo)

[J. 文档](docguide)

[K. PostgreSQL限制](limits)

[L. 首字母缩写词](acronyms)

## [参考书目](biblio)

## [索引](bookindex)




---
{.grid-list}
-  [源码安装](/zh/postgresql/源码安装)
-  [RPM包安装](RPM包安装)
-  [二进制包安装](/zh/postgresql/二进制包安装)
-  [服务器设置和操作](/zh/postgresql/服务器设置和操作)
-  [服务器配置](/zh/postgresql/服务器配置)
-  [客户端认证](/zh/postgresql/客户端认证)
-  [数据库角色](/zh/postgresql/数据库角色)
-  [管理数据库](/zh/postgresql/管理数据库)
-  [本地化](/zh/postgresql/本地化)
-  [日常数据库维护任务](/zh/postgresql/日常数据库维护任务)
-  [备份和恢复](/zh/postgresql/备份和恢复)
-  [高可用,负载均衡和复制](/zh/postgresql/高可用,负载均衡和复制)
-  [监控数据库活动](/zh/postgresql/监控数据库活动)
-  [监控磁盘使用](/zh/postgresql/监控磁盘使用)
-  [可靠性和写前日志](/zh/postgresql/可靠性和写前日志)
-  [逻辑复制](/zh/postgresql/逻辑复制)
-  [准时制(JIT)编译](/zh/postgresql/准时制编译)
-  [回归测试](/zh/postgresql/回归测试)
{.links-list}

