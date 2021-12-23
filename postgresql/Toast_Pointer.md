---
title: 分析Toast Pointer数据块
description: 分析Toast Pointer数据块
published: true
date: 2021-12-23T03:01:22.128Z
tags: toast
editor: markdown
dateCreated: 2021-12-23T03:01:22.128Z
---

# 分析Toast Pointer数据块
## 安装扩展
```bash
create extension pageinspect;
```
借助该扩展，可查看page中各结构体的数值，以及tuple（行）的Raw值（得到Toast Pointer必须查看RAW值，因为通过SQL来查询B字段，将得到Toast Pointer所指向的value，而不是B字段存储的Toast Pointer）。

## 制作测试数据

```sql
test=# create table blog(content text);
test=# select relname,relfilenode,reltoastrelid from pg_class where relname='blog';
 relname | relfilenode | reltoastrelid 
---------+-------------+---------------
 blog    |       16548 |         16551
test=# truncate blog;
test=# insert into blog select repeat(md5(random()::text),1000000) from generate_series(1,1);
```
## 查看chunk_id
```sql
test=# select chunk_id,chunk_seq,length(chunk_data) from pg_toast.pg_toast_16548;
 chunk_id | chunk_seq | length 
----------+-----------+--------
    16554 |         0 |   1988
```
## 查看字段物理和逻辑长度
```sql
test=# select pg_column_size(content) from blog;
 pg_column_size 
----------------
         366340
test=# select length(content) from blog;
  length  
----------
 32000000
```
## 更改存储策略
```sql
test=# alter table blog alter content set storage external;
test=# alter table blog alter content set storage EXTEND;
```
## 查询Toast Pointer 的值
```sql
test=# select lp,t_data from heap_page_items (get_raw_page('blog',0));
\x01120448e80104970500aa400000a7400000
```
## 分析步骤
### 分析原理
从上文选中部分可知，Toast Pointer的大小为18字节，包括变长标头、字段值实际长度、字段值逻辑长度、Toast表OID、Toast Chunk OID 共5个部分。
### 字节转换
先取4个字节0xfc690000。由于X86架构下，使用小端存储，因此内存中的0xfc690000，实际的字节序为0x000069fc， 将其转换为10进制数值
```
转换前
\x0112 0448e801 04970500 aa400000 a7400000
转换后
\x000040a7 000040aa 00059704 01e84804 1201
```
### Toast OID
1. 从t_data 最右边分析
2. OID一般占用4字节
```sql
select x'000040a7'::int;
```
### chunk OID
1. 下4个字节
```sql
select x'000040aa'::int;
```
### 字段值的实际长度
```sql
select x'00059704'::int;
```
### 字段值的逻辑长度
```sql
select x'01e84804'::int;
```

## 结论
字节流（从左往右）


| 字节流（从左往右） |      含义       |
| :---------------: | :-------------: |
|        1-2        |     变长字节     |
|        3-6        | 字段值的实际长度 |
|       7-10        | 字段值的逻辑长度 |
|       11-14       |    chunk_id     |
|       15-16       |  Toast表的OID   |

