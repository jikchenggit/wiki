---
title: mysql安装相关程序
description: mysql安装相关程序
published: true
date: 2021-08-23T03:25:07.070Z
tags: mysql, 安装, 程序
editor: markdown
dateCreated: 2020-03-17T13:50:01.644Z
---

# 1. 安装相关程序

## 1.1. comp_err — Compile MySQL Error Message File

comp_err  创建errmsg  .由于mysqld 使用sys 文件.comp_err  通常在构建MySQL 时自动运行.
## 1.2. mysql_secure_installation — Improve MySQL Installation Security
这个程序可以让你通过以下方式提高MySQL 安装的安全性.

- 可以位根帐号设置密码.
- 可以删除远程主机访问得根账户.
- 可以删除匿名占用.
- 可以删除测试数据库(默认情况下,所有用户都可以访问该数据库,甚至匿名用户账户.

 - 操作流程:

```bash
[root@dbserver ~]# mysql_secure_installation 

Securing the MySQL server deployment.

Enter password for user root: 
The 'validate_password' component is installed on the server.
The subsequent steps will run with the existing configuration
of the component.
Using existing password for root.

Estimated strength of the password: 50 
Change the password for root ? ((Press y|Y for Yes, any other key for No) : n

 ... skipping.
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : yes
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : yes
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y  
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : yes
Success.

All done! 
```
## 1.3. mysql_ssl_rsa_setup — Create SSL/RSA Files

    此程序用于创建SSL 证书和密钥文件以及RSA 密钥对文件,如果这些文件丢失,则需要使用SSL 支持安全连接,并在未加密得连接闪使用RSA 支持安全连接,如果现有SSL 文件过期,还可以使用 mysql_ssl_rsa_setup  创建新的SSL 文件.

:::alert-warning
mysql_ssl_rsa_setup 使用得时openssl 命令,所以他的使用取决于您得机器上是否安装了openssl.
对于使用编译版本得MySQL  还有另外一种生成密钥文件得方式,请参见[使用MySQL创建SSL和RSA证书和密钥.md](file:///D:/SynologyDrive/vnote_notebooks/工作/MySQL/数据库调优/使用MySQL创建SSL和RSA证书和密钥.md)
mysql_ssl_rsa_setup 用于方便快捷的产生SSL 的一种工具. 如果想要更加安全的方式,请从注册证书办法机构获得CA 证书.
:::

-  语法:
```bash
shell> mysql_ssl_rsa_setup [options]
```

典型的选项位:
--datedir 指定何处创建文件, --verbose 查看 mysql_ssl_rsa_setup 调用openssl 的命令.

mysql_ssl_rsa_setup 尝试使用一组默认的文件名创建SSL 和RSA 文件: 工作原理如下.

1. mysql_ssl_rsa_setup 在PATH 环境变量找到指定位置检查openssl 二进制文件.如果没有找到openssl .mysql_secure_installation 将不执行任何操作.如果openssl 存在,mysql_ssl_rsa_setup 将在`--datadir` 指定MySQL 目录中找缺省的SSL 和RSA 文件,如果`--datadir` 选项没有提供则在编译后的数据目录中查找.
2. mysql_ssl_rsa_setup 检查以下文件名是否存在.

```
ca.pem
server-cert.pem
server-key.pem
```

3. 如果这些文件都存在则 mysql_ssl_rsa_setup 将不会创建任何文件.如果不存在,则调用openssl 来创建.
```bash
ca.pem               Self-signed CA certificate
ca-key.pem           CA private key
server-cert.pem      Server certificate
server-key.pem       Server private key
client-cert.pem      Client certificate
client-key.pem       Client private key
```

这些文件将可以使用SSL 加密与客户机的连接.详情请见\<Configuring MySQL to Use Encrypted Connections>

4. mysql_ssl_rsa_setup 检查将检查这些文件是否存在.
```bash
private_key.pem      Private member of private/public key pair
public_key.pem       Public member of private/public key pair
```


5. 如果存在这些文件,mysql_ssl_rsa_setup 将不会创建任何文件.否则,将会调用openssl 去创建.对于通过sha256_password 或caching_sha2_password 插件验证的账户,这些文件支持在未加密的连接闪使用RSA 进行安全密码交换. 详情请见\<SHA-256 Pluggable Authentication\> , \<Caching SHA-2 Pluggable Authentication\>

对于 mysql_ssl_rsa_setup 创建的文件特性的信息,请见: <使用MySQL 创建SSL RSA 证书和密钥>


myqld 在启动时会没有SSL选项时. 自动使用 mysql_secure_installation  创建SSL 文件 来启用SSL . 如果你想显示的指定文件 那么客户端启动参数 `--ssl_cipher` ,`--ssl_cert` ,`--ssl-ley` 选项调用.分别用`ca.epm`,`server-cert.pem` ,`server-key.pem`.

mysqld  启动时 没有显示的RSA 选项,mysql_ssl_rsa_setup 创建RSA 文件启用RSA.


如果服务器启用SSL ,客户端默认使用SSL 连接.但是要显示的指定证书和密钥文件, --ssl-ca, --ssl-cert, and --ssl-key 指定ca.pem, client-cert.pem, and client-key.pem 文件.数据库目录权限通常是系统运行MySQL服务器的系统账户,因此客户机程序不能使用这些文件,请复制到客户机上.

对于本地客户端需要以下三个文件:
```bash
cp ca.pem client-cert.pem client-key.pem ..
```
对于远程客户端,使用安全通道分发文件,确保传输期间不会被篡改. 



如果SSL 文件使用期间已经过期了,可以使用 mysql_secure_installation 设置生成一个新的.
1. 停止mysql 服务器.
2. 重命名 或者删除现有的SSL 文件. (RSA 文件不会过去,所以不需要删除).
3. 调用 mysql_ssl_rsa_setup  调用生成新的SSL 文件.
4. 重启mysql 服务.
mysql_ssl_rsa_setup  支持以下选项

| Format | Description |
| --- | --- |
| [--datadir] | Path to data directory |
| [--help] | Display help message and exit |
| [--suffix]| Suffix for X509 certificate Common Name attribute |
| [--uid] | Name of effective user to use for file permissions |
| [--verbose] Verbose mode |
| [--version] | Display version information and exit |

## 1.4. mysql_tzinfo_to_sql — Load the Time Zone Tables


mysql_tzinfo_to_sql  程序是用来加载操作系统上的时区文件到数据库中去.
举个例子: 每个linux,FreeBSD , Solaris ,and OS X 的操作系统的时区文件目录 大多在: `/usr/share/zoneinfo`  .这个目录叫做zoneinfo 数据库.

如果系统没有zoneinfo 数据库,可以使用<MySQL 服务器时区支持> 章节中下载对应包.

语法:
```bash

shell> mysql_tzinfo_to_sql tz_dir
shell> mysql_tzinfo_to_sql tz_file tz_name
shell> mysql_tzinfo_to_sql --leap tz_file
```

对于第一个语法,将zoneinfo 目录路径传递个msyql_tzinfo_to_sql 并发送到mysql 数据库.
```bash
shell> mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -u root mysql -p
```
加载某个时区文件:并指定时区名称.
```bash
shell> mysql_tzinfo_to_sql tz_file tz_name | mysql -u root mysql
```
如果需要计算闰秒请使用以下语句:
```bash
shell> mysql_tzinfo_to_sql --leap tz_file | mysql -u root mysql
```

可以在MySQL 服务器上看到以下表有数据了:
```bash
mysql> show tables like '%time%';
+---------------------------+
| Tables_in_mysql (%time%)  |
+---------------------------+
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
+---------------------------+
5 rows in set (0.00 sec)
```



## 1.5. mysql_upgrade - 检查和升级表
mysql_upgrade 检查所有数据库中的所有表,看他们是否与当前的MySQL 服务器版本兼容.
mysql_upgrade 还可以升级系统表,能够使用新特性和功能.


如果 mysql_upgrade 找到了不兼容的表,在这个表上进行检查修复.如果无法修复,请手动修复.

MySQL 每次升级都应该执行 mysql_upgrade . 

mysql_upgrade 直接鱼MySQL 服务器通信,向他发送执行升级所需要的SQL语句.

:::alert-warning

Caution

在执行升级之前,应该备份当前的MySQL 安装. 参见<数据库备份方法> 在升级MySQL 安装和运行 mysql_upgrade 之前有些升级不兼容的可能需要特殊处理: [MySQL 5.8.0.13 单机 FOR OEL 7.5 升级、降级.md](file:///D:/SynologyDrive/vnote_notebooks/工作/MySQL/学习教程/Installing%20MySQL%20on%20Linux/MySQL%205.8.0.13%20单机%20FOR%20OEL%207.5%20升级、降级.md)

升级需要启动MySQL 服务器. 升级完成后需要重启MySQL ,以便升级都生效.
:::

If you have multiple MySQL server instances running, invoke mysql_upgrade with connection parameters appropriate for connecting to the desired server. For example, with servers running on the local host on parts 3306 through 3308, upgrade each of them by connecting to the appropriate port:

如果 有多实例的 MySQL 服务器运行,请使用不同的端口连接进行升级.

```bash
shell> mysql_upgrade --protocol=tcp -P 3306 [other_options]
shell> mysql_upgrade --protocol=tcp -P 3307 [other_options]
shell> mysql_upgrade --protocol=tcp -P 3308 [other_options]
```
对于unix 主机使用 `--protocol=tcp` 选项强制tcp/IP 连接.

mysql_upgrade 处理所有的数据库中的所有表,这可能需要很长时间才能完成.每个表都被锁定,所以处理这些表时,其他会话无法使用.检查和修复操作可能非常耗时,特别对于大型表.

检查表的详细步骤和原理请参见: check table 语法.


所有的检查和修复的表使用当前的MySQL 的服务器版本,可以告诉下次服务器相同版本运行 mysql_upgrade 可以告诉用户是否再次检查或修复表.


mysql_upgrade 还将MySQL 版本号 保存在数据库目录 mysql_upgrade_info  文件中,这是用于快速检查是否为此版本检查了所有表. 如果要忽略该文件进行强制检查,请使用`--force` 选项.


mysql_upgrade 检查 mysql.user 表,对于任何plugin 字段为空的行用户,如果密码使用兼容,则设置为`mysql_native_password` . MySQL 4.1 密码行必须手动升级.

mysql_upgrade 不升级帮助表的内容.



mysql_upgrade  检查通用分区程序创建的InnoDB 的表,并尝试将他们升级到 InnoDB 本机分区. 可以使用alter table  进行单独升级.
