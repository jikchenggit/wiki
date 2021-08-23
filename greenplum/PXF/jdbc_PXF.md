---
title: 使用jdbc 连接数据库
description: 
published: true
date: 2021-08-23T04:00:27.834Z
tags: 
editor: markdown
dateCreated: 2021-02-07T15:50:45.754Z
---

# Header
```sql
CREATE EXTERNAL TABLE pxf_test_user(id int,name varchar(44))
            LOCATION ('pxf://pxf.pxf_1?PROFILE=Jdbc&SERVER=default')
            FORMAT 'CUSTOM' (FORMATTER='pxfwritable_import');
```