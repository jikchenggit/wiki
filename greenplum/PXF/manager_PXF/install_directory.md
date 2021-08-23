---
title: 关于PXF的安装和配置目录
description: 关于PXF的安装和配置目录
published: true
date: 2021-08-23T04:00:05.227Z
tags: 关于pxf的安装和配置目录
editor: markdown
dateCreated: 2021-01-29T07:48:38.091Z
---

# 关于PXF的安装和配置目录
安装Greenplum数据库时，PXF安装到主节点和段节点的`$GPHOME/PXF`。如果你安装了PXF rpm或deb包默认位置，PXF会被安装到`/usr/local/PXF-gp<greenplm -major-version>`，或者你选择的目录下(仅适用于`CentOS/RHEL`)。本文档使用`$PXF_HOME`指代PXF安装目录。

# PXF 安装目录
以下目录为安装的目录结构:
|   Directory    |                                     Description                                      |
| -------------- | ------------------------------------------------------------------------------------ |
| apache‑tomcat/ | The PXF Tomcat directory.                                                            |
| bin/           | pxf 执行文件                                                                          |
| commit.sha     | 代码提交版本                                                                          |
| conf/          | 配置目录                                                                              |
| gpextable/     | greenplum 插件,当你使用你`pxf [cluseter register` 命令后将会把插件复制到集群的所有主机上] |
| lib/           | 库文件                                                                               |
| templates/     | 配置文件的模板                                                                        |
| version        | PXF 发布版本                                                                          |

# PXF 运行时生成的目录
在初始化和启动过程中，PXF在$PXF_HOME中创建以下内部目录:
|  Directory   |   Description    |
| ------------ | ---------------- |
| pxf‑service/ | pxf 的实例目录    |
| run/         | 包含PXF pid 文件. |


# PXF用户配置目录

同样，在初始化期间，PXF将通过`PXF_CONF`环境变量指定的用户配置目录与以下子目录和模板文件进行初始化:

| Directory  |                          Description                          |
| ---------- | ------------------------------------------------------------- |
| conf/      | 定义PXF 的配置文件                                             |
| keytabs/   | PXF 服务的kerberos 主题keytab 文件的位置                       |
| lib/       | 默认PXF 的链接库                                               |
| logs/      | PXF 的运行的日志目录.                                          |
| servers/   | 服务配置目录:每个子目录标志一个服务器名称.默认服务器名为`default` |
| templates/ | 连接器服务器模板                                               |
