---
title: 升级或降级mysql
description: 升级或降级mysql
published: true
date: 2021-08-23T03:22:53.605Z
tags: mysql, linux, 升级, 降级
editor: markdown
dateCreated: 2020-03-01T14:43:55.371Z
---

# 1. KVM MySQL 8.0.13 单机 FOR OEL 7.5 升级 、降级

升级操作是日常任务之一，你可以通过升级解决MySQL 版本来解决bug.在生产机器上要注意升级操作的流畅性。
降级操作很少用到，但是特殊时候遇到性能和兼容性问题发生时常常使用降级方式回退。当然也可以使用未被覆盖的升级前的系统。
        NOTE
        > 从8.0 降级到5.7 是不支持的。最好升级前备份你5.7 的数据。
        > 如果需要回退则恢复备份数据即可。
## 1.1. 升级MySQL
### 1.1.1. 升级步骤
- [升级方式]
- [升级途径]
- [升级前准备]
- [验证升级前准备事项]
- [替换升级]
- [升级后调优]
#### 1.1.1.1. Upgrade Methods
- 替换升级：关闭老版本MySQL 数据库，替换老版本的二进制文件或者解压新的MySQL的版本，重新启动MySQL使用已有的数据文件目录，然后运行mysql_upgrade 。对于置换升级过程叫做置换升级。
- 逻辑升级：调用老版本的mysqldump,mysqlpump逻辑导出功能导出数据，安装新的MySQL版本，然后应用导出来的SQL语句到新的MySQL 版本。
    Note
    >  从老版本MySQL 中提取出来的SQL语句应用到新的MySQL版本时，可能会遇到不兼容功能，移除的功能的错误。请提前执行检查这些条目。也可以选择使用checkForServerUpgrade 工具。此工具为SHELL untilites 中提供

- 使用yum 源进行升级。
- 使用apt 进行升级。
### 1.1.2. 升级路径
- 支持升级从MySQL 5.7 到8.0， 升级只支持在通常(GA) 版本之间，对于升级到8.0 则需要5.7 GA 版本（5.7.9 更高），
- 建议升级从最后一个版本升级上来，例如：从MySQL5.7 版本升级到MySQL8.0
- 升级不能跳过版本例如：5.6 到8.0
- 在大版本之内的版本升级是支持的。
### 1.1.3. 升级前准备
1.备份数据
    impartant
> 从8.0 降级到5.7 是不支持的操作。所以要提前备份数据
- MySQL 8.0 整合所有的数据字典数据是在事物表中的。在先前的MySQL版本中，数据字典是被存储到原数据文件中和非事物的系统表中的。升级MySQL5.7 到MySQL8.0 数据字典时从基于文件的架构到数据字典架构。
 在升级以后，数据字典的数据库需要明白操作的不同
 - 检查在升级MySQL8.0 后有什么不同
 - 检查升级后MySQL8.0 添加了什么新功能。
 - 检查升级后MySQL8.0 删除了什么功能。
 - 检查服务和状态变量选项是不是添加或者删除。
 - 如果你的MySQL 包含大量的诗句可能在置换升级后需要大量的时间升级，你可以找个dummy数据库实例来评估升级所需要的时间。拷贝MySQL 实例所有的数据文件，然后在这个dummy 实例上运行升级程序就可以评估升级要多久。
 - 重建和重新安装MySQL 编程语言接口，只要是升级后都要这种操作，都要重新安装语言接口。
     Note
## 1.2. 升级需求验证

1. 必须没有过期的数据类型，过期的函数，孤立的.frm 文件。innodb 表使用非本地分区，触发器丢失或为空
    识别表和触发器是否失败可以运行以下命令
```sh
    mysqlcheck -u root -p --all-databases --check-upgrade
```
输出结果
```sql
Enter password: 
mysql.columns_priv                                 OK
mysql.db                                           OK
mysql.engine_cost                                  OK
mysql.event                                        OK
mysql.func                                         OK
mysql.general_log                                  OK
mysql.gtid_executed                                OK
mysql.help_category                                OK
mysql.help_keyword                                 OK
mysql.help_relation                                OK
mysql.help_topic                                   OK
mysql.innodb_index_stats                           OK
mysql.innodb_table_stats                           OK
mysql.ndb_binlog_index                             OK
mysql.plugin                                       OK
mysql.proc                                         OK
mysql.procs_priv                                   OK
mysql.proxies_priv                                 OK
mysql.server_cost                                  OK
mysql.servers                                      OK
mysql.slave_master_info                            OK
mysql.slave_relay_log_info                         OK
mysql.slave_worker_info                            OK
mysql.slow_log                                     OK
mysql.tables_priv                                  OK
mysql.time_zone                                    OK
mysql.time_zone_leap_second                        OK
mysql.time_zone_name                               OK
mysql.time_zone_transition                         OK
mysql.time_zone_transition_type                    OK
mysql.user                                         OK
sys.sys_config                                     OK
```
2. 查询有没有分区表使用非本地的分区。
```sql
SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES
WHERE ENGINE NOT IN ('innodb', 'ndbcluster')
AND CREATE_OPTIONS LIKE '%partitioned%';
```
3. 查询有没有跟mysql 系统数据库同样的表明
```sql
SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES
WHERE LOWER(TABLE_SCHEMA) = 'mysql'
and LOWER(TABLE_NAME) IN
(
'catalogs',
'character_sets',
'collations',
'column_statistics',
'column_type_elements',
'columns',
'dd_properties',
'events',
'foreign_key_column_usage',
'foreign_keys',
'index_column_usage',
'index_partitions',
'index_stats',
'indexes',
'parameter_type_elements',
'parameters',
'resource_groups',
'routines',
'schemata',
'st_spatial_reference_systems',
'table_partition_values',
'table_partitions',
'table_stats',
'tables',
'tablespace_files',
'tablespaces',
'triggers',
'view_routine_usage',
'view_table_usage'
);
```
如果有相同的表名，请rename table .
4. 检查外键名字是否超过64 字节
```sql
SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_NAME IN
  (SELECT LEFT(SUBSTR(ID,INSTR(ID,'/')+1),
               INSTR(SUBSTR(ID,INSTR(ID,'/')+1),'_ibfk_')-1)
   FROM INFORMATION_SCHEMA.INNODB_SYS_FOREIGN
   WHERE LENGTH(SUBSTR(ID,INSTR(ID,'/')+1))>64);
```
如果有，则改为低于64 字节的外键名字。
5. 升级前确定好哪些功能是在MySQL 8.0 版本中不支持
- 例如MySQL 8.0 不支持MySQL 集群，所以NDB 表必须改为其他存储引擎
- 确定升级后哪些功能进行移除 
# 2. 置换升级
1. 检查升级
2. 检查升级需求验证
3. 确定使用XA 事物，请用XA_COMMIT ,XA ROOLLBAKCK 进行 数据提交或回滚
4. 如果有加密表空间请使用转换key 把加密密钥转换成主密钥
```sql
ALTER INSTANCE ROTATE INNODB MASTER KEY;
```
5. 设置关库模式
innodb_fast_shutdown = 0 时 是关库最慢的方式。会等待事物结束。并且检查点一致时。才能关机。
innodb_fast_shutdown = 1 时 默认选项。不会等待事物结束。但是会检查日志是否全部写入。并且检查点一致。
innodb_fast_shutdown = 2 时 硬关机，相当于断电。关机最快的方法。但是下次启动时。会调用crash recovery  应急修复。

```
SET GLOBAL innodb_fast_shutdown = 1; -- fast shutdown
SET GLOBAL innodb_fast_shutdown = 0; -- slow shutdown

```
6.关闭MySQL 数据库
```sh
mysqladmin -u root -p shutdown
```
7. 替换MySQL 二进制文件。
8. 启动MySQL
```sh
systemctl start mysql
mysqld_safe --user=mysql --datadir=/app/mysql/mysql-files/
```
note:
>如果有加密请使用加密选项指定对应密钥位置。
` --early-plugin-load `

如果启动成功会执行以下操作

   - 在数据目录中，此服务会创建一个目录 叫做backup_metadata_57 且吧db.opt .frm,.par,.TRG,.isl 文件移到这个目录下
    在backup_metadata_57 目录下保留了原先文件系统目录格式，例如t1.frm 在my_schemas 目录下。移动到backup_metadata_57 下也是一样。

   - 在MySQL 数据库升级后，event,proc 表会被改名为event_backup_57 何proc_backup_57库
 
 如果此处出错了，服务会回滚所有在数据目录中的操作，在此时，应该移除所有的redo log 文件，然后启动你的MySQL 5.7 服务在同一数据文件目录下。
 9. 在MySQL 8.0 服务启动成功后执行mysql_upgrade：
```sh
 mysql_upgrade -u root -p
```
msyql_upgrade 检查所有表和所有不相容的内容，此升级程序也会影响information_schema ,和sys 模式
10.  重启数据库生效所有选项
```sh
mysqladmin -u root -p shutdown
mysqld_safe --user=mysql --datadir=/path/to/existing-datadir
```
11. 升级诊断

  - 如果MySQL 服务未启动。请检查是否有旧版本的my.conf 
请检查
```sh
 mysqld --print-defaults
```
如果有错误参数请移除。

  - 启动后发现连接数据库库会导致Commands out of sync or unexpected core dumps 命令未同步，或者不可预料的dump .请检查mysql.h file and libmysqlclient.a  是否为新版本的

 - 如果有自定义函数（UDF） 请重建。

# 3. 升级MySQL 8.0 后的影响
## 3.1.  数据目录改变
MySQL 8.0 是完全由数据字典管理所有数据库对象的。而在MySQL 早期版本。是由源数据文件管理。
## 3.2. caching_sha2_password  插件
caching_sha2_password 和sha256_password 认证插件日工更安全的密码加密。mysql_native_password 插件是较不安全的加密方式。对于sha256_password 加密方式，caching_sha2_password 提供的加密方式性能更好。所以根据性能和安全的策略决定caching_sha2_password 为默认加密方式

- 对于升级后加密方式default_authentication_plugin 系统变量从 mysql_native_password 改变为caching_sha2_password 
    这种改变只是升级到MySQL·8.0 ，新建用户都会使用caching_sha2_password 加密方式，而老用户则不会使用这种方式。
    
 - 查询加密方式
```sql
select Host,User,plugin from user;
```

    改变加密方式
```sql
    ALTER USER user
  IDENTIFIED WITH caching_sha2_password
  BY 'password';
```
- libmysqlclient 库默认登录验证使用的是caching_sha2_password 。
### 3.2.1. caching_shar2_password 兼容性问题 解决
important
> 如果你的MySQL 服务于多个8.0 之前的版本的客户端。则需要设置默认加密方式
```
[mysqld]
default_authentication_plugin=mysql_native_password
```
如果不升级客户端或者添加以上内容则有可能会出现以下错误
```sh
Authentication plugin 'caching_sha2_password' is not supported
Authentication plugin 'caching_sha2_password' cannot be loaded:
dlopen(/usr/local/mysql/lib/plugin/caching_sha2_password.so, 2):
image not found
Warning: mysqli_connect(): The server requested authentication
method unknown to the client [caching_sha2_password]
```
### 3.2.2. 兼容的客户端
```
libmysqlclient  必须 8.0 以上
libmysqlclient 必须mysql 5.7 (23 和以后)
Connector/C++  1.1.11 更高
 Connector/Net 8.0.10 更高
 Connector/Node.js 8.0.9  更高
  the X DevAPI PHP extension (mysql_xdevapi)  支持caching_sha2_password.
```
## 3.3. 配置改变
 1. 重要改变1：
     innodb 现在为MySQL8.0 唯一的表引擎。
     innodb 支持本地分区，但是这个功能在MySQL8.0 中已经不支持了。
     其他表引擎的都要转换为innodb ,分区表 现目前改为非分区表。
     在导入dump 文件中，确保表创建语句没有为支持的数据库引擎。
2. 重要改变2：
     默认字符集已经从latin1 改变为utf8mb4
     character_set_server  变量名改为character_set_database 
     默认变量名collation_server 改为collation_database ，系统变量从latin1_swedish_ci  改为utf8mb4_0900_ai_ci。
     在replication 复制环境中，由于默认字符集改变了 可能会导致问题，所以请手动设置这两个变量
```sh
     [mysqld]
character_set_server=latin1
collation_server=latin1_swedish_ci
```
3. 大小写 
- lower_case_table_names 默认值为0
## 3.4. 服务改变
 - grant 语句不能自动创建用户

## 3.5. innodb 改变
-  很多系统表已经改了名字

| Old Name | New Name |
| --- | --- |
| `INNODB_SYS_COLUMNS` | `INNODB_COLUMNS` |
| `INNODB_SYS_DATAFILES` | `INNODB_DATAFILES` |
| `INNODB_SYS_FIELDS` | `INNODB_FIELDS` |
| `INNODB_SYS_FOREIGN` | `INNODB_FOREIGN` |
| `INNODB_SYS_FOREIGN_COLS` | `INNODB_FOREIGN_COLS` |
| `INNODB_SYS_INDEXES` | `INNODB_INDEXES` |
| `INNODB_SYS_TABLES` | `INNODB_TABLES` |
| `INNODB_SYS_TABLESPACES` | `INNODB_TABLESPACES` |
| `INNODB_SYS_TABLESTATS` | `INNODB_TABLESTATS` |
| `INNODB_SYS_VIRTUAL` | `INNODB_VIRTUAL` | 
# 4. 降级
## 4.1. 数据泵方式
- 只导出某个数据库数据
```sql
mysqldump -u root -p  db_name t1 > dump.sql
mysql -u root -p db_name < dump.sql
```
- 导出所有数据库
```sql
mysqldump -u root -p --all-databases > dump.sql
mysql -u root -p db_name < dump.sql
```
## 4.2. alter table 方式
- 替换了BIN 文件后
- 这种方式使用innodb
 
```sql
 ALTER TABLE t1 ENGINE = InnoDB;
```
- 确定是否是innoddb 表
```sql
SHOW CREATE TABLE
```
## 4.3. repair table 方式
- 修复表
```sql
REPAIR TABLE t1;
```
- 修复某个数据库
```sh
mysqlcheck -u root -p --repair --databases db_name ...
```
- 修复所有数据库
```sh
mysqlcheck -u root -p --repair --all-databases
```
# 5. 远程传输数据库
-  从本地连接到远程数据库进行创建数据库
创建远程用户
```sql
GRANT ALL PRIVILEGES ON *.* TO 'dsg'@'%' IDENTIFIED BY '123' WITH GRANT OPTION;
```

 - 创建数据库
```sh
mysqladmin -h 'other_hostname' -u root -p  create db_name
mysqladmin  -h '192.168.10.141' -u dsg -p create dsg;   
```
 - 从本地传输到远端
```sh
mysqldump db_name | mysql -h 'other_hostname' db_name
mysqldump  -u root -p  dsg    | mysql -h '192.168.10.141' -u dsg -p  dsg;   
```
 - 低带宽的情况下使用（从目标端发起拷贝）
```sh
mysqladmin -u root -p create db_name
mysqldump -h 'other_hostname' -u root -p --compress db_name | mysql db_name
mysqldump  -u root -p  -h '192.168.10.140' --compress dsg    | mysql dsg;   
```

- 生成本地文件
```sh
mysqldump -u root -p --quick db_name | gzip > db_name.gz
mysqldump -u root -p --quick dsg| gzip > dsg.gz
```
 - 导入
```sh
mysqladmin create  dsg
gunzip < dsg.gz | mysql db_name
```

## 5.1. 可以使用mysqldump 、mysqlimport 方式
>这种方式更快
```sh
mkdir DUMPDIR
mysqldump -u root -p --tab=DUMPDIR db_name
mysqldump -u root -p --tab=DUMPDIR dsg
```
导入
```sh
mysqladmin create db_name           # create database
cat DUMPDIR/*.sql | mysql db_name   # create tables in database
mysqlimport db_name DUMPDIR/*.txt   # load data into tables
```
