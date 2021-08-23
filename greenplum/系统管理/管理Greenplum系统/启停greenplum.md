---
title: 启停greenplum
description: 启停greenplum
published: true
date: 2021-08-23T03:45:46.157Z
tags: 启停greenplum
editor: markdown
dateCreated: 2020-08-23T14:26:50.156Z
---

# 启停greenplum

在Greenplum数据库DBMS中，数据库服务器实例(master数据库和所有segment)在系统中的所有主机上启动或停止，这样它们就可以作为一个统一的DBMS一起工作。

由于Greenplum数据库系统分布在许多机器上，启动和停止一个Greenplum数据库系统的过程与启动和停止一个普通的PostgreSQL DBMS的过程是不同的。

分别使用gpstart和gpstop实用程序启动和停止Greenplum数据库。这些实用程序位于Greenplum数据库主主机上的$GPHOME/bin目录中。


> 重要提示:不要发出kill命令来结束任何Postgres进程。相反，使用数据库命令pg_cancel_backend()。

发出`kill -9`或`kill -11`可能会引入数据库损坏，并阻止原因分析。

有关gpstart和gpstop的信息，请参阅[Greenplum数据库实用工具指南]()

# 启动greenplum 数据库

通过在主实例上运行gpstart实用程序来启动一个初始化的Greenplum数据库系统。

使用gpstart实用程序启动一个已经由gpinitsystem实用程序初始化，但已被gpstop实用程序停止的Greenplum数据库系统。gpstart实用程序通过启动Greenplum数据库集群上的所有Postgres数据库实例来启动Greenplum数据库。gpstart编排这个过程并并行执行这个过程。
在master主机上运行gpstart启动Greenplum数据库:
```
$ gpstart
```
# 重启greenplum 数据库

停止`Greenplum`数据库系统，然后重新启动它。
带有`-r`选项的gpstop实用程序可以在关闭完成后停止并重新启动`Greenplum`数据库。
要重启`Greenplum`数据库，请在主主机上输入以下命令:
```
$ gpstop -r
```

# 重新加载配置文件

在不中断系统的情况下重新加载对greenplum 数据库配置文件的更改. 

gpstop实用程序可以重新加载对pg_hba.conf配置文件的更改，以及主postgresql.conf文件和pg_hba.conf文件中的运行时参数，而不会中断服务。活动会话在重新连接到数据库时应用改变的参数。许多服务器配置参数需要重新启动整个系统(`gpstop -r`)才能激活。有关服务器配置参数的信息，请参阅[Greenplum数据库参考指南]()。
重新加载配置文件更改而不关闭系统使用gpstop实用工具:

```
$ gpstop -u
```
# 在维护模式下启动主机

只启动主服务器来执行维护或管理任务，而不会影响段上的数据。

例如，您可以仅在主实例上以维护模式连接数据库，并编辑系统目录设置。有关系统目录表的更多信息，请参阅[Greenplum数据库参考指南]()。

1.使用-m选项运行gpstart:
```
$ gpstart -m
```
2.以维护模式连接到主服务器，以执行目录维护。例如:

```
$ PGOPTIONS='-c gp_session_role=utility' psql postgres
```
3. 完成管理任务后，在实用程序模式下停止master。然后，在生产模式中重新启动它。
```
$ gpstop -mr
```
> 警告:
> 不正确地使用维护模式连接会导致不一致的系统状态。只有技术支持才能执行此操作。
{.is-warning}

# 停止greenplum 数据库

gpstop实用程序停止或重新启动您的Greenplum数据库系统，并始终在主主机上运行。激活后，gpstop将停止系统中的所有postgres进程，包括主实例和所有段实例。gpstop实用程序默认使用最多64个并行工作线程来关闭组成Greenplum数据库集群的Postgres实例。系统在关闭之前等待任何活动事务完成。要立即停止Greenplum数据库，请使用快速模式。

- 停止greenplum 数据库

```
$ gpstop
```
- 在快速模式下停止greenplum 数据库.
```
$ gpstop -M fast
```
默认情况下，如果有任何客户端连接到Greenplum数据库，则不允许关闭该数据库。使用-M快速选项回滚所有正在进行的事务，并在关闭之前终止任何连接。

# 杀死客户端进程

Greenplum数据库为每个客户机连接启动一个新的后端进程。拥有超级用户特权的Greenplum数据库用户可以取消和终止这些客户机后端进程。

使用pg_cancel_backend()函数取消后端进程将结束特定的排队或活动客户端查询。使用pg_terminate_backend()函数终止后端进程将终止到数据库的客户机连接。


pg_cancel_backend()函数有两个传参:
- pg_cancel_backend(pid int4) 
- pg_cancel_backend(pid int4, msg text)

pg_terminate_backend()函数有两个类似的参数;

- pg_terminate_backend( pid int4 )
- pg_terminate_backend( pid int4, msg text )

如果您提供了一个msg, Greenplum数据库将在返回给客户机的取消消息中包含文本。msg被限制为128字节;Greenplum Database会截断任何较长的内容。

pg_cancel_backend()和pg_terminate_backend()函数如果成功将返回true，否则将返回false。

要取消或终止后端进程，必须首先标识后端进程ID。您可以从pg_stat_activity视图的pid列中获取进程ID。例如，要查看与所有正在运行的和排队的查询相关联的流程信息:

```
 SELECT usename, pid, waiting, state, query, datname
     FROM pg_stat_activity;
```
输出
```
 usename |  pid     | waiting | state  |         query          | datname
---------+----------+---------+--------+------------------------+---------
  sammy  |   31861  |    f    | idle   | SELECT * FROM testtbl; | testdb
  billy  |   31905  |    t    | active | SELECT * FROM topten;  | testdb
  ```
  
  使用输出来标识查询或客户端连接的进程id (pid)。
  
  
  
  例如，要取消上面示例输出中标识的等待查询，并包括“Admin cancelled长时间运行的查询”。当回复给客户的信息是:
  
```
  SELECT pg_cancel_backend(31905 ,'Admin canceled long-running query.');
ERROR:  canceling statement due to user request: "Admin canceled long-running query."
```

这样就取消了用户的长时间占用的资源
