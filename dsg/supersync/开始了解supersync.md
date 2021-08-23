---
title: 开始了解supersync
description: 开始了解supersync
published: true
date: 2021-08-23T03:39:01.267Z
tags: 开始了解supersync
editor: markdown
dateCreated: 2020-07-29T08:42:12.369Z
---

 

# 目录下各个文件的意义

dsg@node2:\[/dsg/source/ds11\] tree -L 1
├── bin (软件进程包)
├── config (配置文件)
├── log (软件日志)
├── rmp (中间文件——xf1)
├── scripts (启停脚本)
├── vcfsa (缓存交易)
└── vcfsd (vman注册信息)


dsg@node2:\[/dsg/source/ds11\] tree -L 2
├── bin (软件进程包)
│   ├── aoxd
│   ├── dbpsd (控制进程)
│   ├── vagentd (分析进程)
│   ├── sender (发送进程)
│   ├── oxad (连接ASM)
│   ├── archivelog (日志定时归档进程)
│   ├── vman
│   ├── vm
├── config (配置文件)
│   ├── asm.conf (连接ASM配置文件)
│   ├── ddl.ini (同步哪些ddl)
│   ├── mapping.ini (同步哪些table)
│   ├── redo_ddl_filter.ini (过滤细粒度ddl)
│   └── session_prev.sql (预执行sql)
│   └── .profile_ds (环境变量)
├── log (软件日志)
│   ├── archivelog
│   ├── log.dbpsd
│   ├── log.vagentd
│   ├── log.sender
│   ├── log.oxad.50005
├── rmp (中间文件——xf1)
│   ├── cfg.ctl
│   ├── cfg.finishseq (分析到哪个归档)
│   ├── ddl_dump.txt (同步了哪些ddl)
│   ├── ddl_no_dump.txt (过滤了哪些ddl)
│   ├── r0_t0.txt
│   ├── realwhere.ini
│   ├── 2.cfg.senderno (发送了多少xf1文件)
│   ├── dict (相关数据字典)
│   ├── rowid (CR关系)
├── scripts (启停脚本)
│   ├── check (检查进程启停脚本)
│   ├── clean (将本套软件所有配置信息全部删除，相当于从头再来。)
│   ├── register (注册primary和standby的关联关系)
│   ├── start (启动进程，连带启动oxad进程。)
│   ├── stop (关闭进程，不连带关闭oxad进程。)
│   ├── start_oxad (单独启动oxad进程)
│   ├── stop_oxad (单独关闭oxad进程)
│   ├── mon (检查log目录下的运行日志)
├── vcfsa (缓存交易)
│   ├── vcfs_cvf.db
│   ├── vcfs_dc.db
│   ├── vagentd.pid
│   ├── dirty
│   ├── uncommitted (缓存未commit提交的事务)
│   ├── vcfs_aox_scn.db
└── vcfsd (vman注册信息)
    ├── vcfs_db.db
    ├── vcfs_host.db
    └── vcfs_user.db
    
# 目标端目录架构
 



dsg@node2:\[/dsg/target/dt11\] tree -L 1
├── bin (软件进程包)
├── config (配置文件)
├── fmp (双向同步时，反向映射关系。)
├── imp (激活trigger的脚本，set dict的dmp文件。)
├── log (软件日志)
├── rmp (中间文件——xf1)
├── scripts (启停脚本)
├── vcfsa
└── vcfsd


dsg@node2:\[/dsg/target/dt11\] tree -L 2
├── bin (软件进程包)
│   ├── vagentd (接收进程)
│   ├── loader (装载进程)
│   ├── oxad (连接ASM)
│   ├── archivelog (日志定时归档进程)
├── config (配置文件)
│   ├── asm.conf (连接ASM配置文件)
│   ├── data_load.ini (配置装载参数，例如：索引并发等)
│   ├── real_q.conf (loader -r通道拆分)
│   ├── session_prev.sql (预执行sql)
│   └── tablespace_map.ini (tablespace表空间映射)
├── fmp (双向同步时，反向映射关系。)
│   ├── 4654.xf1
│   ├── 4655.xf1
│   ├── cfg.frmpno (已发送的xf1个数)
│   └── cfg.srmpno (已生成的xf1个数)
├── imp (激活trigger的脚本，set dict的dmp文件。)
│   └── enable_trigger.sql
├── log (软件日志)
│   ├── log.vagentd (接收进程日志)
│   ├── log.r0 (loader -r 增量装载日志)
│   ├── log.s0 (loader -s 全量装载日志)
│   ├── archivelog (日志归档目录)
├── rmp (中间文件——xf1)
│   ├── cfg.objs (object_id和table_name映射)
│   ├── cfg.sync (全量未装载的xf1文件个数)
│   ├── run.flag (1表示全量阶段 2表示增量阶段)
│   ├── cfg.xf1t.struct (用户名、密码、用户名映射)
│   ├── ddl_dump.txt (装载的ddl操作，例如创建sequence)
│   ├── imp_dsg (rowid映射文件目录)
│   ├── sync0 (全量的xf1文件)
│   ├── real0 (增量的xf1文件)
│   ├── run_state.ctl
├── scripts (启停脚本)
│   ├── check (检查进程启停脚本)
│   ├── clean (将本套软件所有配置信息全部删除，相当于从头再来。)
│   ├── start (启动进程，连带启动oxad进程。)
│   ├── stop (关闭进程，不连带关闭oxad进程。)
│   ├── start_oxad (单独启动oxad进程)
│   ├── stop_oxad (单独关闭oxad进程)
│   ├── start_rmp (启动反向映射关系传输进程)
│   ├── stop_rmp (关闭反向映射关系传输进程)
│   ├── stop_v (单独关闭vagentd接收进程)
│   ├── stop_r (单独关闭loader -r进程)
│   ├── stop_s (单独关闭loader -s进程)
│   ├── mon (检查log目录下的运行日志)
├── vcfsa
│   ├── src_total.txt
│   ├── tgt_total
│   └── vagentd.pid
└── vcfsd
    └── ora.pid


 


 