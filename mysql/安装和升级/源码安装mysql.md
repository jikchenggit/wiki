---
title: 源码安装mysql
description: 源码安装mysql
published: true
date: 2021-08-23T03:22:35.176Z
tags: mysql, linux, 安装, 源码
editor: markdown
dateCreated: 2020-03-01T11:36:19.417Z
---

# 3. 使用源码安装部署MYSQL到linux
## 3.1. 安装编译工具

Cmake 可以通过https://cmake.org/download/. 获取
make 安装包http://www.gnu.org/software/make/.
* linux yum 源安装
说明: yum 源要打开ol7_optional_latest选项.
```sh
yum install cmake gcc  make git  bison boost ncurses \
openssl-devel openssl ncurses-devel numactl \
ncurses*i686  libatomic   cyrus-sasl-lib*i686  openldap-devel gcc-c++ libicu* protobuf* \
protobuf-lite libicu libedit*  libaio-devel* autoconf automake m4 libtool make cmake bison gcc
```
 - 由于cmake 版本需要3.2.1 以上版本过低需要升级:

```bash

wget  https://github.com/Kitware/CMake/releases/download/v3.15.1/cmake-3.15.1.tar.gz

```

- make  需要3.75 以上.

```bash
gcc : 5.3 以上.
Clang:3.4 以上(xcode7 或macos)
Solaris Studio :12.4
Visual Studio: 2015
Boost C++ libraries  [http://www.boost.org/](http://www.boost.org/)
CMake  3.2.3
ncurses
```
:::alert-warning
注意:
如果升级了cmake ,gcc ,和make ,请把原有的 rpm  卸载.否则编译得时候会多出步骤,例如指出编译器位置.

:::
## 3.2. 安装标准源码包
### 3.2.1. 增加MySQL组
```sh
groupadd mysql
```
### 3.2.2. 增加MySQL用户
```sh
useradd -r -g mysql -s /bin/false mysql
```
### 3.2.3. 解压源码包
```sh
tar -zxvf mysql-VERSION.tar.gz
```
* note:没有z 选项
```sh
shell> gunzip < mysql-VERSION.tar.gz | tar xvf - 
```

* note:使用CMAKE 解压
```sh
shell> cmake -E tar zxvf mysql-VERSION.tar.gz 
```
### 3.2.4. 创建编译环境
```sh
cd mysql-VERSION
mkdir bld
cd bld
```

NOTE:
> 为了保持源码根目录保持原样。创建新目录进行编译生成的新文件都在bld 里面
### 3.2.5. 配置源码编译目录
```sh
cmake ..
```
* 使用cmake 可以使用以下选项
•	-DBUILD_CONFIG=mysql_release: 配置Mysql版本的
•	-DCMAKE_INSTALL_PREFIX=dir_name: 配置安装目录的前缀。所有的安装文件都会在这个目录下存在
•	-DCPACK_MONOLITHIC_INSTALL=1: 编译出来的文件只有一个
•	-DWITH_DEBUG=1:开启debug 信息。.
•	-DDOWNLOAD_BOOST=1 -DWITH_BOOST=/root/mysql-8.0.12/bld: 下载boost 组件并且下载到bld 目录下.

•	-DWITH_SYSTEMD=1 :使用systemd  方式管理.
:::alert-warning
注意: 使用了systemd  方式后,跟rpm包安装一样,没有msyql_safe 脚本和mysqld_muti
注意:当你如果升级gcc 后安装目录位/usr/local/* 需要指定给cmake
:::
•      -CMAKE_C_COMPILER=/usr/local/gcc
•      -CMAKE_CXX_COMPILER=/usr/local/g++

* 显示显示编译配置信息
```sh
shell> cmake .. -L   # overview 总览
shell> cmake .. -LH  # overview with help text 显示每个选项的帮助
shell> cmake .. -LAH # all params with help text 所有选项的帮助
shell> cmake ..     # interactive display 交互式显示


```


* 范例：

```sh
cmake ..  -DWITH_DEBUG=1 -DCMAKE_INSTALL_PREFIX=/app/mysql -DCPACK_MONOLITHIC_INSTALL=1 -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/root/mysql-8.0.12/bld


cmake ..  -DCMAKE_INSTALL_PREFIX=/app/mysql  -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/root/mysql-8.0.12/bld
# 添加systemd 管理方式,且指定安装目录.
cmake  .. -DWITH_SYSTEMD=1 -DCMAKE_INSTALL_PREFIX=/app/  -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/root/mysql-8.0.15/bld -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ -CMAKE_C_COMPILER=/usr/local/bin/gcc
# 普通编译模式.
cmake  ..  -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/root/mysql-8.0.15/bld 
```
### 3.2.6. 编译

```bash
set(CMAKE_C_COMPILER "/usr/local/gcc")
set(CMAKE_CXX_COMPILER "/usr/local/g++")
```
```sh
shell> make
shell> make VERBOSE=1
#能够显示更多信息.
shell> make -j 20  VERBOSE=1
#如果日志太多不好看,请重定向.
shell> make -j 20   VERBOSE=1 > build.log 2>&1 &
```
NOTE:
>第二个命令显示更多的信息.-j 20 为并发





## 3.3. 安装MySQL
```sh
make install
```
NOTE:
> 指定安装目录前缀，注意如果在cmke 节点已经指定目录，则此为安装目录

*  在此指定安装目录
```sh
shell> make install DESTDIR="/app/mysql"
```
### 3.3.1. 进入默认安装目录
```sh
cd /usr/local/mysql
```

* 创建安全认证目录
```sh
mkdir mysql-files
chown mysql:mysql mysql-files
chmod 750 mysql-files
```
### 3.3.2. 初始化数据库
```sh
shell> bin/mysqld --initialize --user=mysql
```
*  	初始化数据库的两种方式
```sh
shell> bin/mysqld --initialize --user=mysql
shell> bin/mysqld --initialize-insecure --user=mysql
```
Note：
> 两种初始化方式第一种必须强制使用密码登录。
> 第二种方式可以使用跳过密码
`shell> mysql -u root --skip-password`
> 然后更改对应密码
`mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';`
> 在mysql 8.0 默认加密的密码已经改了。现在默认使用caching_sha2_password 加密方式。
如果还需使用默认的mysql_native_password 加密方式的话使用默认 模式。
> 初始化的时候，默认路径为/usr/local/mysql
#### 3.3.2.1. 更改默认路径
* 如果要改变安装目录使用以下目录：
```sh
shell> bin/mysqld --initialize --user=mysql \
         --basedir=/opt/mysql/mysql \
         --datadir=/opt/mysql/mysql/data
```
* 配置文件:
```sh
 [mysqld]
basedir=/opt/mysql/mysql
datadir=/opt/mysql/mysql/data
```
* 使用配置文件指定位置:`
```sh
shell> bin/mysqld --defaults-file=/etc/my.cnf \
         --initialize-insecure --user=mysql
```
*  范例：
```sh
 shell> bin/mysqld --initialize-insecure --user=mysql \
--basedir=/app/mysql \
         --datadir=/app/mysql/mysql-files 
```
*  输出日志：
```sh
[root@mysql1 mysql]# bin/mysqld --initialize-insecure --user=mysql \
> --basedir=/app/mysql \
>          --datadir=/app/mysql/mysql-files
2018-09-28T16:18:32.178288Z 0 [System] [MY-013169] [Server] /app/mysql-commercial-8.0.12-el7-x86_64/bin/mysqld (mysqld 8.0.12-commercial) initializing of server in progress as process 5328
2018-09-28T16:18:46.268910Z 5 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2018-09-28T16:18:57.082553Z 0 [System] [MY-013170] [Server] /app/mysql-commercial-8.0.12-el7-x86_64/bin/mysqld (mysqld 8.0.12-commercial) initializing of server has completed
```
## 3.4. 创建安全连接
* 帮助说明：

| **Format** | **Description** |
| --- | --- |
| [--datadir](file:///D:/refman-8.0-en.html-chapter/programs.html#option_mysql_ssl_rsa_setup_datadir) | Path to data directory |
| [--help](file:///D:/refman-8.0-en.html-chapter/programs.html#option_mysql_ssl_rsa_setup_help) | Display help message and exit |
| [--suffix](file:///D:/refman-8.0-en.html-chapter/programs.html#option_mysql_ssl_rsa_setup_suffix) | Suffix for X509 certificate Common Name attribute |
| [--uid](file:///D:/refman-8.0-en.html-chapter/programs.html#option_mysql_ssl_rsa_setup_uid) | Name of effective user to use for file permissions |
| [--verbose](file:///D:/refman-8.0-en.html-chapter/programs.html#option_mysql_ssl_rsa_setup_verbose) | Verbose mode |
| [--version](file:///D:/refman-8.0-en.html-chapter/programs.html#option_mysql_ssl_rsa_setup_version) | Display version information and exit |

* 默认目录
```sh
shell> bin/mysql_ssl_rsa_setup
```

* 指定目录

```sh
bin/mysql_ssl_rsa_setup --datadir=/app/mysql/mysql-files/

bin/mysql_ssl_rsa_setup --datadir=/app/mysql/data/
```
## 3.5. 安装开发中源码
NOTE:
> 主要介绍怎样从git 中每一个分支中安装MySQL
* 从git 中下载源码
```sh
git clone https://github.com/mysql/mysql-server.git
```
* 进入目录
```sh
cd mysql-server
```
* 查看分支结构
```sh
git branch -r
```
* 查看当前分支
```sh
git branch
```

* 切换分支到8.0
```sh
git checkout 8.0
```
* 检查
```sh
git branch
```
* 切换分支到5.7
```sh
git checkout 5.7
```
* 更新分支
```sh
~/mysql-server$ git checkout 8.0
~/mysql-server$ git pull
```
* 查看日志log
```sh
git log
```
剩下的操作跟标准源码包安装一样。




# 4. 启动和关闭
```sh
 shell> bin/mysqld_safe --user=mysql &
# 6. Next command is optional
```
## 4.1. 拷贝自启动文件
```sh
shell> cp /app/mysql/support-files/mysql.server /etc/init.d/
```
## 4.2. 编辑my.cnf 文件
```sh
basedir=/app/mysql
datadir=/app/mysql/mysql-files
```
## 4.3. 重载启动项目
```sh
systemctl  daemon-reload
```
## 4.4. 查看是否启动MySQL
```sh
systemctl  status mysql
```
## 4.5. 开机自启动
NOTE:
> 由于使用二进制安装,则需要配置chkconfig 。RPM 包不需要
```sh
chkconfig --add mysql.server
chkconfig mysql.server on
```
# 5. MySQL 启动后诊断
## 5.1. 查看日志
```sh
tail host_name.err
```
## 5.2. 选择驱动
默认为InnoDB
## 5.3. 确认数据文件位置是否合适
## 5.4. 查看所有配置参数和所有的环境变量
```sh
mysqld --basedir=/app/mysql --verbose –help | more
```
## 5.5. 配置文件环境变量
```sh
mysqladmin variables
mysqladmin -h host_name variables
```




# 6. 测试MySQL
* 检查mysql 是否正常运行
```sh
mysqladmin version
```

* 检查配置变量值
```sh
mysqladmin variables
```
* 检查是否能登陆
```sh
mysqladmin -u root -p version
```
* 关闭mysql 服务
```sh
mysqladmin -u root shutdown
```

* 显示mysql 数据库
```sh
mysqlshow
```
* 显示某个数据库中的表
```sh
mysqlshow mysql
```
* shell 界面查询表数据
```sh
mysql -e "SELECT User, Host, plugin FROM mysql.user" mysql
```

NOTE:
> 以上命令成功执行后则mysql 数据正常。
# 7. 账户安全
* 使用mysqld --insecure初始化后的密码更改

```sql
mysql -u root -p

ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
```

* 如果使用mysqld --initialize-insecure 进行初始化
则：
```sh
mysql -u root --skip-password
```

* 更改密码：
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '3345091'
```
* 由于密码复杂度有相关要求：但是测试不需要则

```sql
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password.check_user_name    | ON     |
| validate_password.dictionary_file    |        |
| validate_password.length             | 8      |
| validate_password.mixed_case_count   | 1      |
| validate_password.number_count       | 1      |
| validate_password.policy             | MEDIUM |
| validate_password.special_char_count | 1      |
+--------------------------------------+--------+
7 rows in set (0.01 sec)

mysql> set validate_password.policy=0;
ERROR 1229 (HY000): Variable 'validate_password.policy' is a GLOBAL variable and should be set with SET GLOBAL
mysql> set GLOBAL validate_password.policy=0;
```
* 设置密码复杂度为低。更方便测试。

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '12345678';
```



# 8. 编译错误解决
查看CMakeCache.txt 错误全部解决后然后删除CMakeCache.txt 重新cmake 

编译工具无法找到：
可以使用the CMAKE_C_FLAGS and CMAKE_CXX_FLAGS 指定编译工具
```sh
shell> CC=gcc
shell> CXX=g++
shell> export CC CXX
```

确保这个参数在disable 状态下。因为打开这个参数会把警告变成错误。导致编译无法进行编译下去。

如果遇到以下错误
```sh
make: Fatal error in reader: Makefile, line 18:
Badly formed macro assignment
Or:
make: file `Makefile' line 18: Must be a separator (:
Or:
pthread.h: No such file or directory
```

请升级make 版本。确保make 3.75 以上

如果遇到
```sh
"sql_yacc.yy", line xxx fatal: default action causes potential...
```
这个错误
请升级bison older than 1.75 以上

# 9. Doxygen 文档生成
NOTE:
    > 此文档为开发调优时用到的文档，源代码中的组成部件和组成功能都在文档中有体现。
## 9.1. 文档须知需要决定MySQL 的版本
```sh
MYSQL_VERSION_MAJOR=8
MYSQL_VERSION_MINOR=0
MYSQL_VERSION_PATCH=4
MYSQL_VERSION_EXTRA=-rc
```

如果MySQL MySQL不是通用版本，则MYSQL_VERSION_EXTRA 值将不是空的。对于例子，
安装编程辅助工具文档内容
MySQL 源码里包含里使用编程辅助器写的文档，
## 9.2. 安装doxygen 

```sh
yum install doxygen
or 
http://www.stack.nl/~dimitri/doxygen/download.html
```

## 9.3. 下载绘图矢量包
```sh
plantuml.jar
http://plantuml.com/download.html
```
## 9.4. 安装java
```sh
yum install java
```
## 9.5. 启动矢量包服务
```sh
java -jar path-to-plantuml.jar
```
## 9.6. 安装矢量绘图软件graphviz 
```sh
yum install graphviz
or 
官网下载
http://www.graphviz.org/
```

## 9.7. 检测软件是否安装成功
```sh
shell> which dot
/usr/bin/dot
shell> dot -V
dot - graphviz version 2.28.0 (20130928.0220)
```
## 9.8. 配置矢量包路径
```sh
export PLANTUML_JAR_PATH=/root/plantuml.jar
```
## 9.9. 在MySQL 源码根目录创建目录
```sh
mkdir -p generated/doxygen
```
## 9.10. 移动改名Doxyfile
```sh
cp Doxyfile-<fdafd> Doxyfile
```
## 9.11. 生成文档
```sh
doxygen
```
## 9.12. 安装浏览器
```sh
yum install dbus-x11
yum groupinstall 'Fonts'
yum install firefox
```
## 9.13. 启动浏览器开始浏览
```sh
firefox generated/doxygen/html/index.html
```


```sh
firefox @DOXYGEN_OUTPUT_DIR@/html/
```
## 9.14. 官网浏览网址
[https://dev.mysql.com/doc/internals/en/](https://dev.mysql.com/doc/internals/en/)




# 10. 附录
## 10.1. 忘记root 密码

* 方法1：
```sh
kill `cat /mysql-data-directory/host_name.pid`
```

* 把以下语句保存到文件中
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
```
例如：/root/mysql-init
* 开始改变密码
```sh
mysqld --init-file=/home/me/mysql-init &
```
mysql 服务会自动启动。

* 然后删除保存密码的文件。
方法2：
添加my.cnf 选项
```
--skip-grant-tables:
```
* 重启MySQL
* 更改密码
```sql
mysql
FLUSH PRIVILEGES;
update user set password=password("new_pass") where user="root";
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
```
## 10.2. 编译选项参数

| **Formats** | **Description** | **Default** | **Introduced** | **Removed** |
| --- | --- | --- | --- | ---|
| **[BUILD_CONFIG](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_build_config)** | Use same build options as official releases |  |  |  |
| **[BUNDLE\_RUNTIME\_LIBRARIES](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_bundle_runtime_libraries)** | Bundle runtime libraries with server MSI and Zip packages for Windows | **OFF** | 8.0.11 |  |
| **[CMAKE\_BUILD\_TYPE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_cmake_build_type)** | Type of build to produce | **RelWithDebInfo** |  |  |
| **[CMAKE\_CXX\_FLAGS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_cmake_cxx_flags)** | Flags for C++ Compiler |  |  |  |
| **[CMAKE\_C\_FLAGS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_cmake_c_flags)** | Flags for C Compiler |  |  |  |
| **[CMAKE\_INSTALL\_PREFIX](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_cmake_install_prefix)** | Installation base directory | **/usr/local/mysql** |  |  |
| **[COMPILATION_COMMENT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_compilation_comment)** | Comment about compilation environment |  |  |  |
| **[CPACK\_MONOLITHIC\_INSTALL](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_cpack_monolithic_install)** | Whether package build produces single file | **OFF** |  |  |
| **[DEFAULT_CHARSET](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_default_charset)** | The default server character set | **utf8mb4** |  |  |
| **[DEFAULT_COLLATION](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_default_collation)** | The default server collation | **utf8mb4\_0900\_ai_ci** |  |  |
| **DISABLE\_DATA\_LOCK** | Exclude the performance schema data lock instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_COND](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_cond)** | Exclude Performance Schema condition instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_ERROR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_error)** | Exclude the performance schema server error instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_FILE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_file)** | Exclude Performance Schema file instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_IDLE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_idle)** | Exclude Performance Schema idle instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_MEMORY](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_memory)** | Exclude Performance Schema memory instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_METADATA](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_metadata)** | Exclude Performance Schema metadata instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_MUTEX](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_mutex)** | Exclude Performance Schema mutex instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_PS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_ps)** | Exclude the performance schema prepared statements | **OFF** |  |  |
| **[DISABLE\_PSI\_RWLOCK](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_rwlock)** | Exclude Performance Schema rwlock instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_SOCKET](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_socket)** | Exclude Performance Schema socket instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_SP](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_sp)** | Exclude Performance Schema stored program instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_STAGE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_stage)** | Exclude Performance Schema stage instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_STATEMENT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_statement)** | Exclude Performance Schema statement instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_STATEMENT_DIGEST](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_statement_digest)** | Exclude Performance Schema statements_digest instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_TABLE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_table)** | Exclude Performance Schema table instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_THREAD](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_thread)** | Exclude the performance schema thread instrumentation | **OFF** |  |  |
| **[DISABLE\_PSI\_TRANSACTION](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_psi_transaction)** | Exclude the performance schema transaction instrumentation | **OFF** |  |  |
| **[DISABLE_SHARED](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_disable_shared)** | Do not build shared libraries, compile position-dependent code | **OFF** |  |  |
| **[DOWNLOAD_BOOST](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_download_boost)** | Whether to download the Boost library | **OFF** |  |  |
| **[DOWNLOAD\_BOOST\_TIMEOUT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_download_boost_timeout)** | Timeout in seconds for downloading the Boost library | **600** |  |  |
| **[ENABLED\_LOCAL\_INFILE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enabled_local_infile)** | Whether to enable LOCAL for LOAD DATA INFILE | **OFF** |  |  |
| **[ENABLED_PROFILING](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enabled_profiling)** | Whether to enable query profiling code | **ON** |  |  |
| **[ENABLE\_DEBUG\_SYNC](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enable_debug_sync)** | Whether to enable Debug Sync support | **ON** |  | 8.0.1 |
| **[ENABLE_DOWNLOADS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enable_downloads)** | Whether to download optional files | **OFF** |  |  |
| **[ENABLE_DTRACE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enable_dtrace)** | Whether to include DTrace support |  |  | 8.0.1 |
| **[ENABLE\_EXPERIMENTAL\_SYSVARS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enable_experimental_sysvars)** | Whether to enabled experimental InnoDB system variables | **OFF** | 8.0.11 |  |
| **[ENABLE_GCOV](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enable_gcov)** | Whether to include gcov support |  |  |  |
| **[ENABLE_GPROF](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_enable_gprof)** | Enable gprof (optimized Linux builds only) | **OFF** |  |  |
| **[FORCE\_UNSUPPORTED\_COMPILER](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_force_unsupported_compiler)** | Whether to permit unsupported compiler | **OFF** |  |  |
| **[IGNORE\_AIO\_CHECK](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_ignore_aio_check)** | With -DBUILD\_CONFIG=mysql\_release, ignore libaio check | **OFF** |  |  |
| **[INSTALL_BINDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_bindir)** | User executables directory | **PREFIX/bin** |  |  |
| **[INSTALL_DOCDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_docdir)** | Documentation directory | **PREFIX/docs** |  |  |
| **[INSTALL_DOCREADMEDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_docreadmedir)** | README file directory | **PREFIX** |  |  |
| **[INSTALL_INCLUDEDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_includedir)** | Header file directory | **PREFIX/include** |  |  |
| **[INSTALL_INFODIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_infodir)** | Info file directory | **PREFIX/docs** |  |  |
| **[INSTALL_LAYOUT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_layout)** | Select predefined installation layout | **STANDALONE** |  |  |
| **[INSTALL_LIBDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_libdir)** | Library file directory | **PREFIX/lib** |  |  |
| **[INSTALL_MANDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_mandir)** | Manual page directory | **PREFIX/man** |  |  |
| **[INSTALL_MYSQLKEYRINGDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_mysqlkeyringdir)** | Directory for keyring_file plugin data file | **platform specific** |  |  |
| **[INSTALL_MYSQLSHAREDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_mysqlsharedir)** | Shared data directory | **PREFIX/share** |  |  |
| **[INSTALL_MYSQLTESTDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_mysqltestdir)** | mysql-test directory | **PREFIX/mysql-test** |  |  |
| **[INSTALL_PKGCONFIGDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_pkgconfigdir)** | Directory for mysqlclient.pc pkg-config file | **INSTALL_LIBDIR/pkgconfig** |  |  |
| **[INSTALL_PLUGINDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_plugindir)** | Plugin directory | **PREFIX/lib/plugin** |  |  |
| **[INSTALL_SBINDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_sbindir)** | Server executable directory | **PREFIX/bin** |  |  |
| **[INSTALL\_SECURE\_FILE_PRIVDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_secure_file_privdir)** | secure\_file\_priv default value | **platform specific** |  |  |
| **[INSTALL_SHAREDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_sharedir)** | aclocal/mysql.m4 installation directory | **PREFIX/share** |  |  |
| **[INSTALL\_STATIC\_LIBRARIES](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_static_libraries)** | Whether to install static libraries | **ON** |  |  |
| **[INSTALL_SUPPORTFILESDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_install_supportfilesdir)** | Extra support files directory | **PREFIX/support-files** |  |  |
| **[LINK_RANDOMIZE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_link_randomize)** | Whether to randomize order of symbols in mysqld binary | **OFF** | 8.0.1 |  |
| **[LINK\_RANDOMIZE\_SEED](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_link_randomize_seed)** | Seed value for LINK_RANDOMIZE option | **mysql** | 8.0.1 |  |
| **[MAX_INDEXES](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_max_indexes)** | Maximum indexes per table | **64** |  |  |
| **[MUTEX_TYPE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mutex_type)** | InnoDB mutex type | **event** |  |  |
| **[MYSQLX\_TCP\_PORT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mysqlx_tcp_port)** | TCP/IP port number used by X Plugin | **33060** |  |  |
| **[MYSQLX\_UNIX\_ADDR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mysqlx_unix_addr)** | Unix socket file used by X Plugin | **/tmp/mysqlx.sock** |  |  |
| **[MYSQL_DATADIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mysql_datadir)** | Data directory |  |  |  |
| **[MYSQL\_MAINTAINER\_MODE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mysql_maintainer_mode)** | Whether to enable MySQL maintainer-specific development environment | **OFF** |  |  |
| **[MYSQL\_PROJECT\_NAME](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mysql_project_name)** | Windows/OS X project name | **MySQL** |  |  |
| **[MYSQL\_TCP\_PORT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mysql_tcp_port)** | TCP/IP port number | **3306** |  |  |
| **[MYSQL\_UNIX\_ADDR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_mysql_unix_addr)** | Unix socket file | **/tmp/mysql.sock** |  |  |
| **[ODBC_INCLUDES](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_odbc_includes)** | ODBC includes directory |  |  |  |
| **[ODBC\_LIB\_DIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_odbc_lib_dir)** | ODBC library directory |  |  |  |
| **[OPTIMIZER_TRACE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_optimizer_trace)** | Whether to support optimizer tracing |  |  |  |
| **[REPRODUCIBLE_BUILD](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_reproducible_build)** | Take extra care to create a build result independent of build location and time |  | 8.0.11 |  |
| **[SYSCONFDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_sysconfdir)** | Option file directory |  |  |  |
| **[SYSTEMD\_PID\_DIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_systemd_pid_dir)** | Directory for PID file under systemd | **/var/run/mysqld** |  |  |
| **[SYSTEMD\_SERVICE\_NAME](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_systemd_service_name)** | Name of MySQL service under systemd | **mysqld** |  |  |
| **[TMPDIR](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_tmpdir)** | tmpdir default value |  |  |  |
| **[USE\_LD\_GOLD](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_use_ld_gold)** | Whether to use GNU gold loader | **ON** | 8.0.0 |  |
| **[WIN\_DEBUG\_NO_INLINE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_win_debug_no_inline)** | Whether to disable function inlining | **OFF** |  |  |
| **[WITHOUT_SERVER](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_without_server)** | Do not build the server | **OFF** |  |  |
| **[WITHOUT\_xxx\_STORAGE_ENGINE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_storage_engine_options "Storage Engine Options")** | Exclude storage engine xxx from build |  |  |  |
| **[WITH_ANT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_ant)** | Path to Ant for building GCS Java wrapper |  | 8.0.11 |  |
| **[WITH_ASAN](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_asan)** | Enable AddressSanitizer | **OFF** |  |  |
| **[WITH\_ASAN\_SCOPE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_asan_scope)** | Enable AddressSanitizer -fsanitize-address-use-after-scope Clang flag | **OFF** | 8.0.4 |  |
| **[WITH\_AUTHENTICATION\_LDAP](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_authentication_ldap)** | Whether to report error if LDAP authentication plugins cannot be built | **OFF** | 8.0.2 |  |
| **[WITH\_AUTHENTICATION\_PAM](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_authentication_pam)** | Build PAM authentication plugin | **OFF** |  |  |
| **[WITH\_AWS\_SDK](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_aws_sdk)** | Location of Amazon Web Services software development kit |  | 8.0.2 |  |
| **[WITH_BOOST](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_boost)** | The location of the Boost library sources |  |  |  |
| **[WITH\_CLIENT\_PROTOCOL_TRACING](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_client_protocol_tracing)** | Build client-side protocol tracing framework | **ON** |  |  |
| **[WITH_CURL](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_curl)** | Location of curl library |  | 8.0.2 |  |
| **[WITH_DEBUG](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_debug)** | Whether to include debugging support | **OFF** |  |  |
| **[WITH\_DEFAULT\_COMPILER_OPTIONS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_default_compiler_options)** | Whether to use default compiler options | **ON** |  |  |
| **[WITH\_DEFAULT\_FEATURE_SET](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_default_feature_set)** | Whether to use default feature set | **ON** |  |  |
| **[WITH_EDITLINE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_editline)** | Which libedit/editline library to use | **bundled** |  |  |
| **[WITH_ICU](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_icu)** | Type of ICU support | **bundled** | 8.0.4 |  |
| **[WITH\_INNODB\_EXTRA_DEBUG](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_innodb_extra_debug)** | Whether to include extra debugging support for InnoDB. | **OFF** |  |  |
| **[WITH\_INNODB\_MEMCACHED](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_innodb_memcached)** | Whether to generate memcached shared libraries. | **OFF** |  |  |
| **[WITH\_KEYRING\_TEST](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_keyring_test)** | Build the keyring test program | **OFF** |  |  |
| **[WITH_LIBEVENT](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_libevent)** | Which libevent library to use | **bundled** |  |  |
| **[WITH_LIBWRAP](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_libwrap)** | Whether to include libwrap (TCP wrappers) support | **OFF** |  |  |
| **[WITH_LTO](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_lto)** | Enable link-time optimizer | **OFF** | 8.0.13 |  |
| **[WITH_LZ4](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_lz4)** | Type of LZ4 library support | **bundled** |  |  |
| **[WITH_LZMA](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_lzma)** | Type of LZMA library support | **bundled** | 8.0.4 |  |
| **[WITH_MECAB](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_mecab)** | Compiles MeCab |  |  |  |
| **[WITH_MSAN](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_msan)** | Enable MemorySanitizer | **OFF** |  |  |
| **[WITH\_MSCRT\_DEBUG](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_mscrt_debug)** | Enable Visual Studio CRT memory leak tracing | **OFF** |  |  |
| **[WITH_MYSQLX](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_mysqlx)** | Whether to disable X Protocol | **ON** | 8.0.11 |  |
| **[WITH_NUMA](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_numa)** | Set NUMA memory allocation policy |  |  |  |
| **[WITH_PROTOBUF](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_protobuf)** | Which Protocol Buffers package to use | **bundled** |  |  |
| **[WITH_RAPID](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_rapid)** | Whether to build rapid development cycle plugins | **ON** |  |  |
| **[WITH_RAPIDJSON](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_rapidjson)** | Type of RapidJSON support | **bundled** | 8.0.13 |  |
| **[WITH_RE2](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_re2)** | Type of RE2 library support | **bundled** | 8.0.4 |  |
| **[WITH_SSL](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_ssl)** | Type of SSL support | **system** |  |  |
| **[WITH_SYSTEMD](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_systemd)** | Enable installation of systemd support files | **OFF** |  |  |
| **[WITH\_SYSTEM\_LIBS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_system_libs)** | Set system value of library options not set explicitly | **OFF** | 8.0.11 |  |
| **[WITH\_TEST\_TRACE_PLUGIN](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_test_trace_plugin)** | Build test protocol trace plugin | **OFF** |  |  |
| **[WITH_TSAN](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_tsan)** | Enable ThreadSanitizer | **OFF** |  |  |
| **[WITH_UBSAN](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_ubsan)** | Enable Undefined Behavior Sanitizer | **OFF** |  |  |
| **[WITH\_UNIT\_TESTS](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_unit_tests)** | Compile MySQL with unit tests | **ON** |  |  |
| **[WITH_UNIXODBC](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_unixodbc)** | Enable unixODBC support | **OFF** |  |  |
| **[WITH_VALGRIND](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_valgrind)** | Whether to compile in Valgrind header files | **OFF** |  |  |
| **[WITH_ZLIB](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_with_zlib)** | Type of zlib support | **bundled** |  |  |
| **[WITH\_xxx\_STORAGE_ENGINE](file:///D:/refman-8.0-en.html-chapter/installing.html#option_cmake_storage_engine_options "Storage Engine Options")** | Compile storage engine xxx statically into server |  |  |  |

#
