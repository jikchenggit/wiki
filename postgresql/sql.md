---
title: SQL 语言
description: SQL 语言
published: true
date: 2021-08-23T03:55:42.886Z
tags: sql 语言
editor: markdown
dateCreated: 2020-10-26T15:28:45.706Z
---

# 部分 II. SQL 语言

这部份描述在PostgreSQL中SQL语言的使用。我们从描述SQL的一般语法开始，然后解释如何创建保存数据的结构、如何填充数据库以及如何查询它。中间的部分列出了在SQL命令中可用的数据类型和函数。剩余的部分则留给对于调优数据性能的重要方面。

这部份的信息被组织成让一个新用户可以从头到尾跟随它来全面理解主题，而不需要多次参考后面的内容。这些章都是自包含的，这样高级用户可以根据他们的选择阅读单独的章。这一部分的信息被以一种叙事的风格展现。需要查看一个特定命令的完整描述的读者应该去看看[第 VI 部分](reference)。

这一部分的阅读者应该知道如何连接到一个PostgreSQL数据库并且发出SQL命令。我们鼓励不熟悉这些问题的读者先去阅读[第 I 部分](tutorial)。SQL通常使用PostgreSQL的交互式终端psql输入，但是其他具有相似功能的程序也可以被使用。

**目录**

## [4\. SQL语法](sql-syntax)

[4.1. 词法结构](sql-syntax-lexical)

[4.2. 值表达式](sql-expressions)

[4.3. 调用函数](sql-syntax-calling-funcs)

## [5\. 数据定义](ddl)

[5.1. 表基础](ddl-basics)

[5.2. 默认值](ddl-default)

[5.3. 生成列](ddl-generated-columns)

[5.4. 约束](ddl-constraints)

[5.5. 系统列](ddl-system-columns)

[5.6. 修改表](ddl-alter)

[5.7. 权限](ddl-priv)

[5.8. 行安全性策略](ddl-rowsecurity)

[5.9. 模式](ddl-schemas)

[5.10. 继承](ddl-inherit)

[5.11. 表分区](ddl-partitioning)

[5.12. 外部数据](ddl-foreign-data)

[5.13. 其他数据库对象](ddl-others)

[5.14. 依赖跟踪](ddl-depend)

## [6\. 数据操纵](dml)

[6.1. 插入数据](dml-insert)

[6.2. 更新数据](dml-update)

[6.3. 删除数据](dml-delete)

[6.4. 从修改的行中返回数据](dml-returning)

## [7\. 查询](queries)

[7.1. 概述](queries-overview)

[7.2. 表表达式](queries-table-expressions)

[7.3. 选择列表](queries-select-lists)

[7.4. 组合查询](queries-union)

[7.5. 行排序](queries-order)

[7.6. `LIMIT`和`OFFSET`](queries-limit)

[7.7. `VALUES`列表](queries-values)

[7.8. `WITH`查询（公共表表达式）](queries-with)

## [8\. 数据类型](datatype)

[8.1. 数字类型](datatype-numeric)

[8.2. 货币类型](datatype-money)

[8.3. 字符类型](datatype-character)

[8.4. 二进制数据类型](datatype-binary)

[8.5. 日期/时间类型](datatype-datetime)

[8.6. 布尔类型](datatype-boolean)

[8.7. 枚举类型](datatype-enum)

[8.8. 几何类型](datatype-geometric)

[8.9. 网络地址类型](datatype-net-types)

[8.10. 位串类型](datatype-bit)

[8.11. 文本搜索类型](datatype-textsearch)

[8.12. UUID类型](datatype-uuid)

[8.13. XML类型](datatype-xml)

[8.14. JSON 类型](datatype-json)

[8.15. 数组](arrays)

[8.16. 组合类型](rowtypes)

[8.17. 范围类型](rangetypes)

[8.18. 域类型](domains)

[8.19. 对象标识符类型](datatype-oid)

[8.20. pg_lsn 类型](datatype-pg-lsn)

[8.21. 伪类型](datatype-pseudo)

## [9\. 函数和操作符](functions)

[9.1. 逻辑操作符](functions-logical)

[9.2. 比较函数和操作符](functions-comparison)

[9.3. 数学函数和操作符](functions-math)

[9.4. 字符串函数和操作符](functions-string)

[9.5. 二进制串函数和操作符](functions-binarystring)

[9.6. 位串函数和操作符](functions-bitstring)

[9.7. 模式匹配](functions-matching)

[9.8. 数据类型格式化函数](functions-formatting)

[9.9. 时间/日期函数和操作符](functions-datetime)

[9.10. 枚举支持函数](functions-enum)

[9.11. 几何函数和操作符](functions-geometry)

[9.12. 网络地址函数和操作符](functions-net)

[9.13. 文本搜索函数和操作符](functions-textsearch)

[9.14. XML 函数](functions-xml)

[9.15. JSON 函数和操作符](functions-json)

[9.16. 序列操作函数](functions-sequence)

[9.17. 条件表达式](functions-conditional)

[9.18. 数组函数和操作符](functions-array)

[9.19. 范围函数和操作符](functions-range)

[9.20. 聚集函数](functions-aggregate)

[9.21. 窗口函数](functions-window)

[9.22. 子查询表达式](functions-subquery)

[9.23. 行和数组比较](functions-comparisons)

[9.24. 集合返回函数](functions-srf)

[9.25. 系统信息函数和运算符](functions-info)

[9.26. 系统管理函数](functions-admin)

[9.27. 触发器函数](functions-trigger)

[9.28. 事件触发器函数](functions-event-triggers)

[9.29. Statistics Information Functions](functions-statistics)

## [10\. 类型转换](typeconv)

[10.1. 概述](typeconv-overview)

[10.2. 操作符](typeconv-oper)

[10.3. 函数](typeconv-func)

[10.4. 值存储](typeconv-query)

[10.5. `UNION`、`CASE`和相关结构](typeconv-union-case)

[10.6. `SELECT`的输出列](typeconv-select)

## [11\. 索引](indexes)

[11.1. 简介](indexes-intro)

[11.2. 索引类型](indexes-types)

[11.3. 多列索引](indexes-multicolumn)

[11.4. 索引和`ORDER BY`](indexes-ordering)

[11.5. 组合多个索引](indexes-bitmap-scans)

[11.6. 唯一索引](indexes-unique)

[11.7. 表达式索引](indexes-expressional)

[11.8. 部分索引](indexes-partial)

[11.9. 只用索引的扫描和覆盖索引](indexes-index-only-scans)

[11.10. 操作符类和操作符族](indexes-opclass)

[11.11. 索引和排序规则](indexes-collations)

[11.12. 检查索引使用](indexes-examine)

## [12\. 全文搜索](textsearch)

[12.1. 介绍](textsearch-intro)

[12.2. 表和索引](textsearch-tables)

[12.3. 空值文本搜索](textsearch-controls)

[12.4. 额外特性](textsearch-features)

[12.5. 解析器](textsearch-parsers)

[12.6. 词典](textsearch-dictionaries)

[12.7. 配置例子](textsearch-configuration)

[12.8. 测试和调试文本搜索](textsearch-debugging)

[12.9. GIN 和 GiST 索引类型](textsearch-indexes)

[12.10. psql支持](textsearch-psql)

[12.11. 限制](textsearch-limitations)

## [13\. 并发控制](mvcc)

[13.1. 介绍](mvcc-intro)

[13.2. 事务隔离](transaction-iso)

[13.3. 显式锁定](explicit-locking)

[13.4. 应用级别的数据完整性检查](applevel-consistency)

[13.5. 提醒](mvcc-caveats)

[13.6. 锁定和索引](locking-indexes)

## [14\. 性能提示](performance-tips)

[14.1. 使用`EXPLAIN`](using-explain)

[14.2. 规划器使用的统计信息](planner-stats)

[14.3. 用显式`JOIN`子句控制规划器](explicit-joins)

[14.4. 填充一个数据库](populate)

[14.5. 非持久设置](non-durability)

## [15\. 并行查询](parallel-query)

[15.1. 并行查询如何工作](how-parallel-query-works)

[15.2. 何时会用到并行查询？](when-can-parallel-query-be-used)

[15.3. 并行计划](parallel-plans)

[15.4. 并行安全性](parallel-safety)
