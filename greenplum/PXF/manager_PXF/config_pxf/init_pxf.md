---
title: 初始化pxf
description: 初始化pxf
published: true
date: 2021-08-23T04:00:20.640Z
tags: 初始化pxf
editor: markdown
dateCreated: 2021-01-29T08:03:13.460Z
---

# 初始化PXF
PXF服务器是一个Java应用程序。必须显式初始化PXF Java服务实例。这种一次性初始化将PXF扩展文件复制到Greenplum数据库安装，创建PXF服务web应用程序，并生成PXF配置文件和模板。

PXF提供了两个可以用于初始化的管理命令:
- `pxf cluster init` —初始化Greenplum数据库集群中的所有pxf服务实例

- `pxf init` —初始化当前Greenplum数据库主机上的pxf服务实例

PXF还提供了类似的重置命令，您可以使用这些命令重置您的PXF配置。

> 本主题中的过程假设您已经将`$PXF_HOME/bin`目录添加到$PATH中
{.is-warning}

# 配置属性
PXF支持内部和用户自定义配置属性.
PXF内部配置文件位于PXF安装中的`$PXF_HOME/conf`目录下。

在PXF初始化时，可以通过一个名为`$PXF_CONF`的环境变量来识别PXF用户配置目录。如果在初始化PXF之前没有设置`$PXF_CONF`，则在初始化过程中，PXF可能会提示您接受或拒绝默认的用户配置目录`$HOME/ PXF`。


> 注意:选择一个您可以备份的`$PXF_CONF`目录位置，并确保它位于Greenplum数据库安装目录之外。
{.is-success}

有关`$PXF_CONF`子目录及其内容的列表，请参阅[PXF用户配置目录](/greenplum/PXF/manager_PXF/install_directory)。

# 初始化概述

PXF服务器运行在Java 8或11上。您可以在PXF初始化时识别PXF `$JAVA_HOME`和`$PXF_CONF`设置。

## 初始化PXF:
- 当设置了`$GPHOME`环境变量时，将PXF扩展名文件从`$PXF_HOME/gpextable`复制到Greenplum数据库安装目录。在init命令中指定——skip-register选项，指示PXF跳过这个初始化步骤。
- 创建PXF Java web应用程序。
- 生成PXF内部配置文件，针对您的配置设置默认属性。

初始化PXF也会创建`$PXF_CONF`用户配置目录(如果它不存在的话)，然后用以下方式填充conf和templates子目录:

- 用户可自定义的PXF运行时和日志配置设置文件
- 模板/ -模板配置文件

PXF通过更新`$PXF_CONF/conf/PXF-env.sh`用户配置文件中同名的属性，记住在初始化期间指定的`JAVA_HOME`设置。PXF在启动时启动这个环境文件，允许它使用不同于系统默认Java的Java安装运行。
如果初始化时指定的`$PXF_CONF`目录已经存在，则PXF只更新templates子目录和`$PXF_CONF/conf/ PXF-env.sh`环境配置文件。
# 预备知识

在您的greenplum 数据库集群中初始化PXF 之前,请确保:

- 您的Greenplum数据库集群已经启动并运行。
- 您已经确定了PXF用户配置目录文件系统位置$PXF_CONF，并且gpadmin用户具有创建或写入该目录所需的权限。
- 您可以为PXF识别Java 8或11 $JAVA_HOME设置。 

# 操作流程

执行以下步骤在Greenplum数据库集群中的每个段主机上初始化PXF。

1. 登录到Greenplum数据库主节点:

```bash
$ ssh gpadmin@<gpmaster>
```
2. 在您的shell中export  JAVA_HOME设置。例如:
```bash
gpadmin@gpmaster$ export JAVA_HOME=/usr/lib/jvm/jre
```
3. 执行`pxf cluster init`命令在主备主机和每个网段主机上初始化pxf服务。例如，以下命令指定`/usr/local/greenplum-pxf`作为PXF用户配置目录进行初始化:

```bash
gpadmin@gpmaster$ PXF_CONF=/usr/local/greenplum-pxf pxf cluster init
```

> 注意:PXF服务只在网段主机上运行。但是，`pxf cluster init`还在Greenplum数据库master主机和slave用主主机上设置pxf用户配置目录。
{.is-warning}

# 重置PXF
如果需要，可以将PXF重置为未初始化状态。如果指定的`PXF_CONF`目录不正确，或者希望从头开始初始化过程，则可以选择重置PXF。

重置PXF时，PXF提示确认操作。如果您确认，PXF删除以下运行时文件和目录:

- $PXF_HOME/conf/pxf-private.classpath
- $PXF_HOME/pxf-service
- $PXF_HOME/run


在重置操作期间，PXF不会删除$PXF_CONF目录，也不会删除可能已复制到Greenplum数据库安装中的任何PXF扩展文件。


在主机上重置PXF之前，必须先停止网段主机上的PXF服务实例。

## 过程

执行以下步骤在Greenplum数据库集群中的每个段主机上重置PXF。

1. 登录到Greenplum数据库主节点:

```bash
$ ssh gpadmin@<gpmaster>
```
2. 停止每个网段主机上的PXF服务实例:
```bash
gpadmin@gpmaster$ pxf cluster stop
```
3. 重置所有Greenplum主机上的PXF服务实例:

```bash
gpadmin@gpmaster$ pxf cluster reset
```
> 注意:重置PXF后，必须初始化并启动PXF才能再次使用服务。
{.is-warning}




