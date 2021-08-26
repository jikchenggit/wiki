---
title: 升级一个postgresql
description: 
published: true
date: 2021-08-26T06:10:16.038Z
tags: 升级postgresql
editor: markdown
dateCreated: 2021-08-26T06:10:16.038Z
---

# 升级一个postgresql
主要描述了如何降数据库数据从一个`postgresql`版本升级到一个新的版本.

postgresql 版本号包裹一个主版本号和一个副板本号.例如,在版本号10.1 中,10 是主版本号,1是次版本号,这表示这是主版本的第一个修复版本呢,对于postgres 10.0 之前的版本,版本号未三个数字组成,例如: 9.5.3 . 主版本号为前两位,后面一位是次版本号. 

小版本号永远不会改变内部存储格式,并总是和主版本号的向上和向下兼容. 例如: 10.1 版本 兼容10.0 和10.6 版本.类似的.9.5.3 兼容9.5.0 和9.5.1 和9.5.6 . 在兼容版本(主版本号)更新,只需要在服务器关闭时替换可执行文件并重启服务器. 数据目录保持不变- 这是小版本升级,是比较简单.

对于postgresql 的主要版本,内部数据存储格式可能会发生变化,因此升级会变得比较复杂. 将数据移动到新的主版本传统方法事是逻辑导出数据,并把数据重新加载到新数据库,这种方法在数据量比较大的情况下比较慢 . 所以建议使用`pg_upgrade` 方式进行在线升级. 使用逻辑复制也可以进行迁移.

新的主版本通常还会引入一些新功能,移除一些功能,可能需要修改应用程序做对应调整.

所以在完全切换到新版本志之前,你需要重新部署一套新版本,进行应用测试.postgresql 升级时主要考虑以下的变化.

- DBA 级别
在每个主版本中,管理员监视和控制服务器的功能会发生变化和改进.

- SQL
通常情况下,新的SQL 命令功能在发布说明中特别提到. 

- 库的API
像libpq的API 库只会添加新的功能,除非在发布说明中特别提到.

- 系统数据字典
系统字段更改通常只会影响数据管理工具.

- 服务器C 语言API
涉及到后端函数API 的更改,此API 时C 百年城语言编写的. 这样的更改会影响服务器内部引用后台函数的代码.

# 通过pg_dumpall 升级数据库

这种升级方法是导出postgresql 主版本的数据,然后把数据导入到另一个版本中重新加载数据,可以使用pg_dumpall 这样的逻辑备份工具;文件系统级别的方法 是无法进行升级的. 

由于pg_dumpall 导出的数据为标准SQL ,可以使用此程序进行升级,降级到7.0 版本以前的任何版本.

# 数据迁移步骤

1. 如果要进行备份,请确保数据库没有更新操作.因为增量数据不会包含在备份文件中,迁移数据后会有遗漏. 
备份命令:
```bash
pg_dumpall > outputfile
```
2. 停止postgresql 数据库
```bash
pg_ctl stop
```
或者使用绝对路径进行启停.

```bash
/etc/rc.d/init.d/postgresql stop
```
启动停止命令请参考[server-start](/postgresql/runtime/server-start)
3. 移动老版本二进制文件到形目录
```bash
mv /usr/local/pgsql /usr/local/pgsql.old
```
4. 安装新版本的postgresql 参考[installation](/postgresql/installation)

5. 创建一个新的数据库集簇.并配置老用户.
```
/usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
```
6. 恢复pg_hba.conf 和 postgresql.conf 的文件 .


7. 启动数据库实例,
```bash
/usr/local/pgsql/bin/postgres -D /usr/local/pgsql/data
```
8. 最后,恢复数据
```bash
/usr/local/pgsql/bin/psql -d postgres -f outputfile
```
9. 当然可以安装两套postgresql 数据库进行两个数据库时时迁移.

```bash
pg_dumpall -p 5432 | psql -d postgres -p 5433
```
# 通过pg_upgrade 进行数据迁移
`pg_upgrade` 程序允许安装新程序,然后使用`--link` 程序的方式进行,postgresql 数据库几分钟之内就可以升级完毕.
