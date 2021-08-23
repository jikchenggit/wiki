---
title: 使用systemd方式管理mysql
description: 使用systemd方式管理mysql
published: true
date: 2021-08-23T03:22:42.449Z
tags: mysql, systemd, 管理
editor: markdown
dateCreated: 2020-03-01T11:37:41.753Z
---

# 1. 使用systemd 管理MySQL
如果使用通用二进制安装发行版安装MySQL ,可以参考安装章节配置systemd 对mysql 服务进行支持.
如果使用源码版本进行安装请添加编译项`-DWITH_SYSTEMD=1`  才能够使用systemd 进行管理

> 注意: 在`systemd` 类型的平台上安装MySQL ,`mysql_safe` 脚本是不需要的.所有的自动重启和诊断`systemd` 都已经替代了`mysql_safe`脚本.
因为`systemd` 支持管理多个MySQL 实例,所以`mysqld_multi` 和 `mysqld_multi.server` 都没必要装.
{.is-warning}




## 1.1. 认识systemd 
- systemctl   用法:

```bash
systemctl {start|stop|restart|status} mysqld
```

- system v 系统方式的用法:
```bash
service mysqld {start|stop|restart|status}
```

- 对于systemd 的文件支持:
- mysqld.server :systemd 的配置文件. 关于怎么详细配置mysql服务启动诊断.
- mysqld@.server :systemd 的配置文件. 关于怎么详细配置mysql.是管理多个MySQL实例.
- mysqld.tmpfiles.d :包含tpmfiles 特性的文件,文件使用mysql.conf 名称安装.
- mysqld_pre_systemd :配置创建错误日志文件,日志位置路径为(/var/log/myql/mysql*.log)



## 1.2. 配置systemd 的MySQL 支持
使用systemd 来管理MySQL 有三种方法.

- 使用本地的systemd 配置文件.

- 设置systemd 为服务器进程设置的环境变量.

- 设置mysql_ots systemd 变量.

使用本地化的systemd 配置文件,创建'/etc/systemd/system/mysqld.service.d 目录.如果它不存在.在该目录中,创建一个包含设置[Service] 的文件.

```
[Service]
LimitNOFILE=*`max_open_files`*
PIDFile=*`/path/to/pid/file`*
Nice=*`nice_level`*
LimitCore=*`core_file_limit`*
Environment="LD_PRELOAD=*`/path/to/malloc/library`*"
Environment="TZ=*`time_zone_setting`*"
```

以下命令会打开一个oerride.conf 文件.
The discussion here uses `override.conf` as the name of this file. Newer versions of systemd support the following command, which opens an editor and permits you to edit the file:

```bash
systemctl edit mysqld  # RPM platforms
systemctl edit mysql   # Debian platforms
```

Whenever you create or change `override.conf`, reload the systemd configuration, then tell systemd to restart the MySQL service:

```bash
systemctl daemon-reload
systemctl restart mysqld  # RPM platforms
systemctl restart mysql   # Debian platforms
```
override.conf 配置方法中必须要用到某些参数,而不是MySQL 选项文件中的[mysqld]或mysqld_safe]


- 对于某些参数,`override.conf` 必须知名.systemd 本身要知道这些值,但是无法从MySQL 选项文件中获取.

 - 对于systemd 的参数必须指定,因为systemd 也不会从mysql_safe 中读取文件.


你可以在`override.conf` 文件中设置以下参数.

- 指定进程id 文件. PIDFile 和ExcStart. MySQL 选项文件中的进程ID 文件设置都会被忽略.
    
```bash
    [Service]
    PIDFile=/var/run/mysqld/mysqld-custom.pid
    ExecStart=
    ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld-custom.pid $MYSQLD_OPTS
```
- 要限制MySQL 可用文件打开数量,要在overrid.conf 中使用`LimitNOFILE` 而不是 `--open-files-limit`选项.
    
-  要设置core 文件生产的大小,请使用  `LimitCore` 而不是` --core-file-size`
    
- 设置调度优先级使用`Nice`  而不是 `--nice`.
    

一些MySQL 参数是使用环境变量配置的.

- `LD_PRELOAD`: 设置特定的内存分配,设置此变量.
    
- `NOTIFY_SOCKET`: 这个环境变量指定msyqld 的套接字.用于systemd 的通信启动完成和服务状态.
- `TZ`: 设置此变量指定服务器的默认时区.    
    

很多方法可以指定环境变量值,systemd 管理mysql 时使用.


- 使用`Environment`  指定MySQL 的环境变量.

- 使用/etc/sysconfig/mysql 文件(如果不存在,手动创建.)指定环境变量值.

```bash

    LD_PRELOAD=*`/path/to/malloc/library`*
    TZ=*`time_zone_setting`*
```

在更改/etc/sysconfig/mysql 后,重启服务器生效

```bash
    systemctl restart mysqld  # RPM platforms
    systemctl restart mysql   # Debian platforms
```
- 不直接修改systemd 配置文件的情况下,使用systemctl 命令行设置环境变量.

```bash
systemctl set-environment MYSQLD\_OPTS="--general\_log=1"
systemctl unset-environment MYSQLD_OPTS
```

`MYSQLD_OPTS` 也可以设置为`/etc/sysconfig/mysql` 文件.

修改之后重启服务.
```bash

systemctl restart mysqld  # RPM platforms
systemctl restart mysql   # Debian platforms
```

对于systemd 平台,如果服务器启动时为空,则初始化数据目录.
如果数据目是暂时消失的远程挂载.会出现问题,挂载点显示为一个空数据数据目录,然后初始化为一个新的数据目录.要抑制这种初始化的行为.使用`/etc/sysconfig/mysql` 文件中指定以下行

```bash
NO_INIT=true
```
## 1.3. 使用systemd  配置多个MySQL 实例
详细描述了怎么使用systemd  配置多个MySQL 实例.
:::alert-warning

Note
由于systemd  能够管理和配置多个MySQL 实例.所以msyql_multi 没有必要进行安装
:::
- 配置位置:
- `/etc/my.cnf` or `/etc/mysql/my.cnf` (RPM platforms)

- `/etc/mysql/mysql.conf.d/mysqld.cnf` (Debian platforms)

例如:想要两个`replicat01` 和`replica02` 可以在选项文件中添加如下内容.

RPM platforms:

```

[mysqld@replica01]
datadir=/var/lib/mysql-replica01
socket=/var/lib/mysql-replica01/mysql.sock
port=3307
log-error=/var/log/mysqld-replica01.log

\[mysqld@replica02\]
datadir=/var/lib/mysql-replica02
socket=/var/lib/mysql-replica02/mysql.sock
port=3308
log-error=/var/log/mysqld-replica02.log
```

Debian platforms:

```bash

[mysqld@replica01]
datadir=/var/lib/mysql-replica01
socket=/var/lib/mysql-replica01/mysql.sock
port=3307
log-error=/var/log/mysql/replica01.log

\[mysqld@replica02\]
datadir=/var/lib/mysql-replica02
socket=/var/lib/mysql-replica02/mysql.sock
port=3308
log-error=/var/log/mysql/replica02.log
```

这里显示复制名称使用"@" 作为分隔符,这是systemd 支持的唯一分隔符.

然后使用systemd  进行实例管理.
```bash
systemctl start mysqld@replica01
systemctl start mysqld@replica02
```
:::alert-warning
note:
   1. 启动之前需要关闭selinux 否则,MySQL 会出现文件目录权限不足的情况.
```
2019-07-29T06:11:04.168545Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.17) initializing of server in progress as pro
cess 5680
2019-07-29T06:11:14.545811Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: ha8jsdPZuN+W
2019-07-29T06:11:19.545373Z 0 [System] [MY-013170] [Server] /usr/sbin/mysqld (mysqld 8.0.17) initializing of server has completed
2019-07-29T06:11:23.035233Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.17) starting as process 5731
2019-07-29T06:11:23.049201Z 1 [ERROR] [MY-012592] [InnoDB] Operating system error number 13 in a file operation.
2019-07-29T06:11:23.049244Z 1 [ERROR] [MY-012595] [InnoDB] The error means mysqld does not have the access rights to the directory.
2019-07-29T06:11:23.049281Z 1 [ERROR] [MY-012270] [InnoDB] os_file_get_status() failed on './ibdata1'. Can't determine file permission
s
2019-07-29T06:11:23.049354Z 1 [ERROR] [MY-010334] [Server] Failed to initialize DD Storage Engine
2019-07-29T06:11:23.049464Z 0 [ERROR] [MY-010020] [Server] Data Dictionary initialization failed.
2019-07-29T06:11:23.049583Z 0 [ERROR] [MY-010119] [Server] Aborting
2019-07-29T06:11:23.050267Z 0 [System] [MY-010910] [Server] /usr/sbin/mysqld: Shutdown complete (mysqld 8.0.17)  MySQL Community Serve
r - GPL.
```
   2. 启动后自动初始化每个实例的目录且安装相关数据文件.
   3.  管理多个实例要把[mysqld] ,[mysql_safe] 配置文件全部注释,否则,会读取这些变量值. 
:::

如若要开机自启动:
```bash
systemctl enable mysqld@replica01
systemctl enable mysqld@replica02
```

 systemd 支持通配符操作.
```bash

systemctl status 'mysqld@replica*'
```

对于同一台机器上有多个MySQL 实例管理, systemd自动使用不同的管理文件

- `mysqld@.service` rather than `mysqld.service` (RPM platforms)

- `mysql@.service` rather than `mysql.service` (Debian platforms)

在管理文件中,`%I` 和`%I` 引用在'@' 标记之后的传入参数,并用于管理特定的实例.

```bash
systemctl start mysqld@replica01
```
systemd 中的管理文件使用这样的命令行进行启动服务器.

```bash
mysqld --defaults-group-suffix=@%I ...
```

结果是`[server]`, `[mysqld]`, and `[mysqld@replica01]` 配置中的参数将会用于启动的实例.

Note

在Debian 平台上,AppArmor 阻止在`/var/lib/mysql-replica` 目录之外创建数据文件.如果要解决这个问题.你必须在`/etc/apparmor.d/usr.sbin.mysqld` 中自定义或禁用配置文件.

Note

在Debian 平台上,无法使用MySQL 卸载脚本处理`msyqld@"实例"` .在删除或升级包之前,必须手动停止任何的额外的实例.

## 1.4. 从msyqld_safe 迁移到systemd 上.

请参照第一章节进行systemd  mysql.server 服务编写.
