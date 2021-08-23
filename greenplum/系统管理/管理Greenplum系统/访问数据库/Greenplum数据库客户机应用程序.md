---
title: Greenplum数据库客户机应用程序
description: Greenplum数据库客户机应用程序
published: true
date: 2021-08-23T03:46:21.170Z
tags: greenplum数据库客户机应用程序
editor: markdown
dateCreated: 2020-08-23T14:45:40.299Z
---

# Greenplum数据库客户机应用程序
Greenplum数据库与许多位于Greenplum数据库主主机安装的$GPHOME/bin目录中的客户机实用程序一起安装。以下是最常用的客户端实用程序:

|    Name    |                 Usage                  |
| ---------- | -------------------------------------- |
| createdb   | create a new database                  |
| createlang | define a new procedural language       |
| createuser | define a new database role             |
| dropdb     | remove a database                      |
| droplang   | remove a procedural language           |
| dropuser   | remove a role                          |
| psql       | PostgreSQL interactive terminal        |
| reindexdb  | reindex a database                     |
| vacuumdb   | garbage-collect and analyze a database |


> 注意:createlang和droplang是在未来的版本可能被删除。这些实用程序不再用于的Greenplum数据库，如果试图使用它们来安装Greenplum过程语言，它们将显示一个错误,请使用“CREATE EXTENSION”或“DROP EXTENSION”命令。

在使用这些客户机应用程序时，必须通过Greenplum master实例连接到数据库。您需要知道目标数据库的名称、主机名和主机端口号，以及要连接的数据库用户名。可以在命令行上分别使用-d、-h、-p和-U选项提供这些信息。如果发现一个不属于任何选项的参数，它将首先被解释为数据库名称。


所有这些选项都有默认值，如果没有指定该选项，将使用这些默认值。默认主机是本地主机。默认端口号是5432。默认用户名是您的OS系统用户名，也是默认数据库名。注意，操作系统用户名和Greenplum数据库用户名不一定相同。

如果默认值不正确，可以将环境变量PGDATABASE、PGHOST、PGPORT和PGUSER设置为适当的值，或者使用psql ~/。包含常用密码的pgpass文件。

有关Greenplum数据库环境变量的信息，请参阅Greenplum数据库参考指南。有关psql的信息，请参阅Greenplum[数据库实用工具指南]()。