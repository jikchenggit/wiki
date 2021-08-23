---
title: 使用gpbackup和gprestore进行并行备份
description: 使用gpbackup和gprestore进行并行备份
published: true
date: 2021-08-23T03:49:37.623Z
tags: 使用gpbackup和gprestore进行并行备份
editor: markdown
dateCreated: 2020-08-27T03:57:50.522Z
---

# 使用gpbackup和gprestore进行并行备份

gpbackup和gprestore是Greenplum数据库实用程序，它们为Greenplum数据库创建和恢复备份集。默认情况下，gpbackup只将用于备份的对象元数据文件和DDL文件存储在Greenplum数据库主数据目录中。Greenplum数据库段使用`COPY ... ON SEGMENT `命令存储他们的数据备份表在压缩的CSV数据文件，位于每个段的备份目录下。

备份元数据文件包含gprestore在并行恢复完整备份集时所需的所有信息。备份元数据还提供了一个框架，用于在gprestore的未来版本中仅恢复数据集中的单个对象以及任何依赖对象。(有关更多信息，请参阅[理解备份文件](/zh/greenplum/系统管理/管理Greenplum系统/备份恢复数据).在CSV文件中存储表数据还提供了使用其他恢复工具(如gpload)在同一个集群或另一个集群中加载数据的机会。默认情况下，为段中的每个表创建一个文件.可以使用gpbackup指定 `--leaf-partition-data `选项，为分区表的每个分区创建一个数据文件，而不是单个文件。此选项还允许您按分区筛选备份集。


每个gpbackup任务使用Greenplum数据库中的单个事务。在此事务期间，元数据在master主机上备份，每个段主机上每个表的数据使用 `COPY ... ON SEGMENT`命令并行。备份进程在备份的每个表上获得访问共享锁。


有关gpbackup和gprestore实用工具选项的信息，请参阅[gpbackup]()和[gprestore]()。

# 备份的前置条件

	gpbackup和gprestore实用程序与以下Greenplum数据库版本兼容:
  
  - Greenplum Database 4.3.22以及以后的版本
  - 关键的Greenplum数据库5.5.0或更高版本
  - 关键的Greenplum数据库6.0.0或更高版本
  gpbackup和gprestore有以下限制:
  
  如果在父分区表上创建索引，gpbackup不会在父分区表的子分区表上备份相同的索引，因为在子分区表上创建相同的索引会导致错误。但是，如果交换分区，gpbackup不会检测交换分区上的索引是否从新的父表继承。在这种情况下，gpbackup将备份产生冲突的CREATE INDEX语句，这会在恢复备份集时导致错误。
  
  您可以执行gpbackup的多个实例，但是每次执行都需要不同的时间戳。
  
  数据库对象筛选目前仅限于模式和表.
  
  当备份部分或所有叶分区与根分区处于不同模式的分区表时，叶分区表定义(包括模式)将作为元数据备份。即使备份操作指定应该排除包含叶分区的架构，也会发生这种情况。在这种情况下，要控制为这种类型的分区表备份的数据，可以使用`--leaf-partition-data `选项。
  
  如果没有指定`--leaf-partition-data `选项，即使备份操作指定应该排除叶分区模式，也会备份叶分区数据。
  
  如果指定了`--leaf-partition-data `选项，那么如果备份操作指定应该排除叶分区模式，则不会备份叶分区数据。只备份叶分区表的元数据。
  
  如果使用`gpbackup——single-data-file`选项将表备份合并到每个段的单个文件中，则无法使用gprestore执行并行恢复操作(不能将`——jobs`设置为高于1的值)。
  
  不能将`——exclude-table`文件与`gpbackup——single-data-file`一起使用。尽管可以在`—exclude-table-file`指定的文件中指定叶分区名，但gpbackup会忽略分区名。
  
  在运行DDL命令的同时使用gpbackup备份数据库可能会导致gpbackup失败，以确保备份集中的一致性。例如，如果在备份操作开始后删除了一个表，gpbackup退出并显示错误消息error: relation <schema。表>不存在。
  
  当在备份操作期间由于表锁定问题而删除表时，gpbackup可能会失败。gpbackup生成要备份的表列表，并获得对这些表的访问共享锁。如果表上持有排他锁，gpbackup会在现有锁被释放后获得访问共享锁。如果当gpbackup试图获取表上的锁时，该表不再存在，那么gpbackup退出并显示错误消息。
  
  当在备份操作期间由于表锁定问题而删除表时，gpbackup可能会失败。gpbackup生成要备份的表列表，并获得对这些表的访问共享锁。如果表上持有排他锁，gpbackup会在现有锁被释放后获得访问共享锁。如果当gpbackup试图获取表上的锁时，该表不再存在，那么gpbackup退出并显示错误消息。
  
  对于在备份期间可能被删除的表，可以使用gpbackup表筛选选项(如——exclude-table或——exclude-schema)从备份中排除这些表。
  
  用gpbackup创建的备份只能恢复到具有与源集群相同数量的段实例的Greenplum数据库集群。如果运行gpexpand将新段添加到集群，则在展开完成后无法恢复展开之前所做的备份。
  
# 包含在备份或恢复中的对象
下表列出了使用gpbackup和gprestore备份和恢复的对象。为使用——dbname选项指定的数据库备份数据库对象。默认情况下，全局对象(Greenplum Database system对象)也会备份，但只有在gprestore中包含——with-globals选项时才会恢复它们。

**表1。备份和还原的对象**

|                                                                                                                                                                                                                                                                                                         Database (for database specified with --dbname)                                                                                                                                                                                                                                                                                                          |                                                                 Global (requires the --with-globals option to restore)                                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| - Session-level configuration parameter settings (GUCs)- Schemas, see [Note](#topic_x3s_lqj_tbb__schema_note)- Procedural language extensions- Sequences- Comments- Tables- Indexes- Owners- Writable External Tables (DDL only)- Readable External Tables (DDL only)- Functions- Aggregates- Casts- Types- Views- Materialized Views (DDL only)- Protocols- Triggers. (While Greenplum Database does not support triggers, any trigger definitions that are present are backed up and restored.)- Rules- Domains- Operators, operator families, and operator classes- Conversions- Extensions- Text search parsers, dictionaries, templates, and configurations | - Tablespaces- Databases- Database-wide configuration parameter settings (GUCs)- Resource group definitions- Resource queue definitions- Roles- GRANT assignments of roles to databases |
