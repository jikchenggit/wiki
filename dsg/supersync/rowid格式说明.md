---
title: rowid格式说明
description: rowid格式说明
published: true
date: 2021-08-23T03:39:07.808Z
tags: rowid格式说明
editor: markdown
dateCreated: 2020-07-29T14:31:11.024Z
---

# 1. ROWID 说明
定义：
> Rowid 看似像表的某个字段，但是实际数据并不存在表里，可查询，并不能删除或更新。
 - Rowid 的格式:
 
```sql
SQL> SELECT ROWID FROM employees WHERE employee_id = 100;
 
ROWID
------------------
AAAR3kAAEAAAKS8AAA
```

![rowid.png](/supersync/rowid.png)
 

 
Rowid 分为四部分
```
four-piece format, OOOOOOFFFBBBBBBRRR,
```

OOOOOO：

- 为对象ID.
FFF：
- 为数据文件编号
BBBBBB：
- 为块编号
- RRR
在这个数据块里的行编号
 
- Rowid 变化情景:
Case1: rowid move 打开（默认）
主键更新，闪回表操作，倒入导出，移动表空间都会改变ROWID.
Case1: rowid move 未打开
导入导出会变化
