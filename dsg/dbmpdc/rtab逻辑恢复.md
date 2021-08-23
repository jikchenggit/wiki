---
title: rtab逻辑恢复
description: rtab逻辑恢复
published: true
date: 2021-08-23T03:30:27.944Z
tags: 逻辑恢复, rtab
editor: markdown
dateCreated: 2020-04-09T07:36:10.432Z
---

# 1. 使用rtab 进行抽取
## 1.1. 概述:

### 1.1.1. 版本优化

#### 1.1.1.1. 6.2.1.4 
这个版本的dbpsd开始，有一个集成odcx功能，让逻辑恢复更简单！



> 1、这个功能储存卷必须和dbpsd在同一台机器上面！
> 2、dbpsd必须和实际运行的dbpsd -home ... 这个在一台机器上面。
{.is-warning}



**范例：**
```bash
./dbpsd -rtab 6005 1.1 DSG.T1 /tmp/1.xf1 -v0123
```

连接`dbpsd`端口为`6005`,
恢复版本为`1.1`，
恢复表为`DSG.T1` 
输出xf1文件为：`/tmp/1.xf1` 
` -v0123`为调试参数。


> 1. 其实就是把sfscp输出sfs.ini和具体文件.ini，以及调用odcx抽取表这三个功能集成到一起了
> 2. 如果表明为大小写敏感,请使用以下语句
{.is-warning}


如：DSG.tx1表
```bash
./dbpsd -rtab 6005 1.1 DSG.\"tx1\" /tmp/1.xf1 -v0123
```
:::
#### 1.1.1.2. 6.2.1.8 版本说明
dbpsd这个版本开始: 6.2.1.8 对-rtab功能做了部分优化。

1. sfscp输出的文件列表以前会有日志文件名，其实没用的，现在自动给去掉了。
2. 增加sfs模块最开始加载索引并发，测试发现数据文件非常多的时候-rtab加载sfs部分索引功能非常慢，这个地方现在调整为并发加载多个数据文件索引。

3. 默认为4个并发，这个暂时调整不了。
```bash
16:51:13 [sfs] check bid 1.1 oid list with threads 4 ...
```

4. 增加odcx输入参数：
```bash
       -rtab dbpsd_port version owner.table xf1_filename odcx_parameters
                       : call odcx restore table data to xf1 file.
```
**工程范例：**

```bash
/tmp/dbpsd 3105 1.1 DSG.T1 /tmp/1.xf1 "-p 6"
```
对应这个-p 6参数就会传入给odcx:
```bash
/tmp/dbpsd -c_odcx /tmp/dsg1684b9067241.tmp -t DSG.T1 -o /tmp/1.xf1 -p 6
```
#### 1.1.1.3. 6.2.6.4 版本

**概述:**
这个版本dbpsd开始对-rtab参数做了部分扩展。

1. 支持对sfs.ini两个参数传入。

如：
```bash
       -rtab dbpsd_port version owner.table xf1_filename odcx_parameters sfs_parameters
                       : call odcx restore table data to xf1 file.
                               EX: -rtab 4005 1.1 DSG.T1 /dsg/t1.xf1 "-p 3" "12,2"
```

##### 1.1.1.3.1. sfs_parameters 部分参数含义：

- check_thread值是sfs.ini的SFS部分值，可以提高逻辑恢复最开始部分并发加载文件索引信息。默认为4，最大24。

- BIM的hrcnt值如果遇到单个文件特别大（8K数据块）的时候需要调整该值，要不用默认就可以。默认为2，支持最大数据文件65G(数据块大小为8K)

这些信息调整后在dbpsd逻辑恢复最开始部分会输出提示信息，自己多观察看看有什么区别。
##### 1.1.1.3.2. 原理说明
odcx_parameters参数设置是dbpsd直接传入给odcx的，dbpsd不会做修改。dbpsd添加-v0123可以看到dbpsd实际逻辑恢复的时候执行的shell命令具体是什么。
如：
```bash
2019-09-29:17:53:32 create file `/tmp/dsg16d7c7085bb0.tmp` for odcx.
2019-09-29:17:53:32 sh>[./dbpsd -c_sfscp -f /tmp/dsg16d7c7085ba1.tmp -ls >/tmp/dsg16d7c7085bb0.tmp 2>/dev/null]
2019-09-29:17:53:32 call odcx export table `DSG.T1` -> `/tmp/1.xf1` ...
2019-09-29:17:53:32 sh>[./dbpsd -c_odcx /tmp/dsg16d7c7085bb0.tmp -t DSG.T1 -o /tmp/1.xf1 -p 3]
```

由于有odcx_parameters参数的存在，也就是说你可以将你想要的odcx参数传入给后面执行的dbpsd -c_odcx部分。

范例如下：
```bash
./dbpsd -rtab 4005 1.1 "DSG.T1" /tmp/1.xf1 "-p 3 -u DSG" -v0123
```
对应odcx部分执行shell就是：
```bash
2019-09-29:17:53:32 sh>[./dbpsd -c_odcx /tmp/dsg16d7c7085bb0.tmp -t DSG.T1 -o /tmp/1.xf1 -p 3 -u DSG]
```
也就是说变向到达到的直接恢复某个用户下面所有变的功能。【odcx -u DSG表示恢复DSG用户下面所有表。】
要恢复多个用户下面所有表：`-u DSG2,DSG1`

要恢复多个不同表：`-t DSG.T1,DSG.T2`
对应dbpsd命令：`./dbpsd -rtab 4005 1.1 "DSG.T1,DSG.T2" /tmp/1.xf1 "-p 3" -v0123`

更多用法自己看看odcx -h
### 1.1.2. temp 目录设置
要改变这个/tmp目录，通过环境变量DBPS_TMP来改变，不设置DPPS_TMP环境变量，默认临时目录存放在/tmp下面。

## 1.2. 逻辑恢复方案:
目前能想到的就这三种路子，
1、按现在这种方式rtab一个一个抽；
2、从备份集恢复出系统表空间数据文件+该删除用户数据所在表空间文件，然后直接odcx数据抽取；
3、从备份集恢复系统表空间数据文件，结合客户在线数据文件进行数据抽取

本文档为方案1.

## 1.3. 更换 最新版. 
- 需要获取以下最新版.

```ogg
dbpsd ,vagendc  # 放再备份服务端/bin
```

```ogg
yloader ,oxad  ## 放在生产服务端/bin
```

> 获取在 151 机器上gan 目录.
> 注意替换 18 年以后得版本.
> 如果是老版本,请执行以下命令.
{.is-warning}



 - 刷新vmf.

```bash
cp vcfs_vmf.db vcfs_vmf.db.bak
./dbpsd -home /biapp1_1/vcfs1 -rebuild_vmf -v0123
```
## 1.4. 启动存储池

 替换相关版本后.  启动需要恢复的存储池.



## 1.5. rtab 的执行流程
1. dbpsd自动创建sfs.ini，存放到：
```bash
create sfs.ini -> /tmp/dsg16d7e21c7921.tmp 
```
这个文件名是 dbpsd随机产生的，dbpsd会保证他的唯一性。
同时
```bash
export SFS_CONF_FILENAME=/tmp/dsg16d7e21c7921.tmp 
```

2. 输出odcx需要的文件。
```bash
./dbpsd -c_sfscp -f /tmp/dsg16d7e21c7921.tmp -ls >/tmp/dsg16d7e21c7930.tmp 2>/dev/null
```

3. 执行odcx恢复：
例子：
```bash
./dbpsd -c_odcx /tmp/dsg16d7e24d8d00.tmp -t DSG.T1 -o /tmp/1.xf1
```


## 1.6. rtab 得语法规范
```bash
：
-rtab dbpsd_port version owner.table xf1_filename odcx_parameters sfs_parameters
: call odcx restore table data to xf1 file.
EX: -rtab 4005 1.1 DSG.T1 /dsg/t1.xf1 "-p 3" "12,2"
```



> sfs_parameters部分参数含义：
> 1.  check_thread值,BIM部分hrcnt值
> 
> check_thread值是sfs.ini的SFS部分值，可以提高逻辑恢复最开始部分并发加载文件索引信息。默认为4，最大24。
> 
> 2. BIM的hrcnt值如果遇到单个文件特别大（8K数据块）的时候需要调整该值，要不用默认就可以。默认为2，支持最大数据文件65G(数据块大小为8K)
{.is-info}



>  odcx_parameters参数设置是dbpsd直接传入给odcx的，dbpsd不会做修改。dbpsd添加-v0123可以看到dbpsd实际逻辑恢复的时候执行的shell命令具体是什么。
> 
> 
> 如：
>
> ```log
> 2019-09-29:17:53:32 create file `/tmp/dsg16d7c7085bb0.tmp` for odcx.
> 2019-09-29:17:53:32 sh>[./dbpsd -c_sfscp -f /tmp/dsg16d7c7085ba1.tmp -ls >/tmp/dsg16d7c7085bb0.tmp 2>/dev/null]
> 2019-09-29:17:53:32 call odcx export table `DSG.T1` -> `/tmp/1.xf1` ...
> 2019-09-29:17:53:32 sh>[./dbpsd -c_odcx /tmp/dsg16d7c7085bb0.tmp -t DSG.T1 -o /tmp/1.xf1 -p 3]
> ```
> 
> 由于有odcx_parameters参数的存在，也就是说你可以将你想要的odcx参数传入给后面执行的dbpsd -c_odcx部分。
> 如：
> ```bash
> ./dbpsd -rtab 4005 1.1 "DSG.T1" /tmp/1.xf1 "-p 3 -u DSG" -v0123
> ```

> 对应odcx部分执行shell就是：
> ```bash
> 2019-09-29:17:53:32 sh>[./dbpsd -c_odcx /tmp/dsg16d7c7085bb0.tmp -t DSG.T1 -o /tmp/1.xf1 -p 3 -u DSG]
> ```
> 也就是说变向到达到的直接恢复某个用户下面所有表的功能。【odcx -u DSG表示恢复DSG用户下面所有表。】
{.is-info}


-  要恢复多个用户下面所有表：`-u DSG2,DSG1`
-  要恢复多个不同表：`-t DSG.T1,DSG.T2`
- 对应dbpsd命令：`./dbpsd -rtab 4005 1.1 "DSG.T1,DSG.T2" /tmp/1.xf1 "-p 3" -v0123`

## 1.7. 批量脚本范例:

```bash
export DBPS_HOME=$DBPS_BASE/bin
export DBPS_TMP=/biapp1_2/tmp

./dbpsd2 -rtab 8405 1.2 "KPI.LAYOUT_PARAM,KPI.LOG_APP_DOWNLOAD,KPI.LOG_APP_WORK_TOOLS" /biapp1_2/rec/1.xf1 "-p 24" "12,2" -v0123 > log_all.log 2>&1 &
```

> 说明:
> 1. 尽可能调大并发参数进行并发扫描.
> 2. 导出来的1.xf1 包含所有的表数据. 请使用dbpsd 的yloader 方式装载.ximp  有问题.
> 3. 如果报错为:
> ```bash
> (call T#1-bim:api/put.c:57), [ODCX]-BIM-100 BIM maximum support block# 3932159>2747136
> ```
> 请适当调大 `"12,2"` 参数.
> 
> 4. 临时目录需要指定一个大点的地方 最好是备份存储池.
> 5. 此脚本可以批量恢复表.
{.is-warning}



# 2.  odcx方式抽取.
## 2.1. 配置sfs 文件

```bash
[dsg@bak1:/dsg/newsnap/SnapAssure/11g/bin]$cat sfs.ini
# 2. shared file system
module=SFS 
  dbps_host=135.24.253.31 # dbpsd host 
  dbps_port=8405 # dbpsd port 
 # aiod_port=8989 # aiod port 
  work_blen=200M # sfsd work buffer length
  work_bid=1.2 # sfsd work bid
  #check_dbf=Y
  print=3 # 调整越大输出的调试信息越多，如：1,2,3
  check_thread=24           # 这个是 检查并发并发越高 扫描越快.check files thread count. (range: 1 ~ 24)
   

# 3. backup file index management
module=BIM 
  blen=200M # maximum buffer length 
  hrcnt=2 # .hix relative block count 调整块大小
  drcnt=50 # .dix relative block count 调整块大小.
  mfno=20480 # maximum support file count
  fpc=64 # file pool count
  home=/dsg/newsnap/SnapAssure/11g/bin
```


> BIM的参数必须在sfs最开始时候修改,后期修改无效.
{.is-info}


dbps_host：存储池对应后台dbpsd所在主机信息
dbps_port：dbpsd运行时所用端口号
work_bid：所需恢复的表数据所在的备份集版本号，比如1.1,1.2,1.3


dbps_host：存储池对应后台dbpsd所在主机信息
dbps_port：dbpsd运行时所用端口号
work_bid：所需恢复的表数据所在的备份集版本号，比如1.1,1.2,1.3

## 2.2. 生成备份数据库实例的数据文件列表信息
```bash
./dbpsd -c_sfscp -f sfs.ini -ls > sfs_dbf.txt
```

> 注：此步骤中生成的sfs_dbf.txt文件中会有控制文件和在线日志的文件信息，一定要将其删除掉，只留dbf文件列表，空行也要删掉，否则逻辑恢复生成xf1文件时会报错。
> 合规的文件列表举例（只有dbf文件，只有dbf文件，只有dbf文件）：
{.is-info}


```bash
[oracle@localhost bin]$ cat sfs_dbf.txt 
/u01/oracle/oradata/orcl/system01.dbf
/u01/oracle/oradata/orcl/sysaux01.dbf
/u01/oracle/oradata/orcl/undotbs01.dbf
/u01/oracle/oradata/orcl/users01.dbf
[oracle@localhost bin]$
```
## 2.3. 声明sfs.ini存放路径环境变量
```bash
export SFS_CONF_FILENAME=/dsg/yanbs/asm/server/SnapAssure/11g/bin/sfs.ini
```

## 2.4. 生成需要恢复逻辑表的xf1文件
```bash
./dbpsd  -c_odcx  sfs_dbf.txt -t DSG.T1 -o ./1.xf1
```


## 2.5. 脚本
```bash
export DBPS_HOME=$DBPS_BASE/bin
export SFS_CONF_FILENAME=$DBPS_BASE/bin/sfs.ini

nohup ./dbpsd2 -c_odcx -o path:///biapp1_2/recvory -t KPI.LAYOUT_PARAM,KPI.LOG_APP_DOWNLOAD,KPI.LOG_APP_WORK_TOOLS,KPI.LOG_APP_WORK_TOOLS_DETAIL,KPI.LOG_RESULT_POINTER_DETAIL,KPI.LOG_TO_ALL,KPI.MAP_CHANNEL_INFO,KPI.MAP_CHANNEL_INFO_LOG,KPI.MAP_CHANNEL_KEYAREA,KPI.MAP_CHANNEL_PARSON sfs_dbf.txt > log1.log 2>&1  &
wait

nohup ./dbpsd2 -c_odcx -o path:///biapp1_2/recvory -t KPI.MAP_CHANNEL_SOUCER_CODE,KPI.MAP_CHENNEL_TYPE_CODE,KPI.MAP_CONTRACT_INFO,KPI.MAP_EXP_CHANNEL_INFO,KPI.MAP_KEY_AREA_CODE,KPI.MAP_PINGJIA,KPI.MESSAGES_INFO,KPI.MID_CB_GRGAN_DEFAULT,KPI.MID_CHNL_RES_DATA_FILE_LIST,KPI.MID_COMM_YJ_JS_HD_M  sfs_dbf.txt  > log2.log 2>&1  &
wait
```

参数说明: 
```
-o path:///biapp1_2/recvory 为路径
-t 为表名.
sfs_dbf.txt : 生成得数据文件列表.
```

# 3. 装载方案
## 3.1. ximp 方案
```bash
./bin/ximp dsg/sjbf#?69 -tablemap "LAYOUT_PARAM LAYOUT_PARAM_NEW"    /dsg/recvory/LAYOUT_PARAM_NEW.xf1 
```

**只有yloader 方案.**

## 3.2. yloader 配置

```ogg
cat /tmp/yloader.ini 
module=YLD
  home=/tmp/y1 #设置yloader临时工具目录
  table_exists_do=drop #如果目标端表存在就drop掉
 #map=DSG.T:DSG.T1 #类似之前的tablemap
#map=*:DSG #所有的表都映射到DSG这个用户下面

module=YXAC
  service= 192.168.1.251,8981 # 连接oxad的host & port，需要将xf1文件装载入库到哪个主机，对应主机要启动oxad程序供yloader装载数据入库，oxad临时进程启动方式：./oxad -startup -n 8981 -flog /tmp/oxad.log &
  db_host=/u01/app/oracle/product/11.2.0/db_1 # $ORACLE_HOME
  db_user=dsg #登录Oracle的用户名
  db_pwd=dsg #登录Oracle的用户的密码
  db_name=db11 #ORACLE_SID
  encrypt_pwd=n #登录密码是否加密存放？pwdcrypt工具可以加密

```


> 注：无论xf1文件在哪个主机，你只需要在此xf1文件所在主机放置一个dbpsd可执行程序并且在需要导入表数据的主机启动oxad进程，并将oxad进程信息（host和port）写入与dbpsd同主机的yloader.ini里面，直接执行`./dbpsd -c_yloader -f ./yloader.ini -simp ./1.xf1`将xf1文件数据装载入库即可。
{.is-info}





## 3.3. map 通配符使用

yloader -v 1.1.0.5 32 bit (QA), build# 6 这个版本的yloader开始对map=???做了扩展。


map可以写成类似下面：
```ogg
map=DS.*:DT.DB1_*
```

表示源端所有DS用户下的表，映射到目标的DT用户，在表名前添加DB1_，可以实现批量修改表名！

## 3.4. 启动相同版本的oxad

```bash
nohup ./oxad2 -startup -n 10.79.240.11:8503 -flog /tmp/oxad.log &
```
## 3.5. 装载命令

```bash
nohup  ./yloader -f /tmp/yloader.ini -simp /dsg/SnapAssure/11g/1.xf1 > log1.log 2>&1 &
或
nohup ./dbpsd -c_yloader -f ./yloader.ini -simp ./1.xf1
```


