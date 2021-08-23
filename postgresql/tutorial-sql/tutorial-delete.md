---
title: 删除
description: 删除
published: true
date: 2021-08-23T03:54:16.667Z
tags: 删除
editor: markdown
dateCreated: 2020-10-26T15:14:01.828Z
---

## 2.9. 删除



数据行可以用`DELETE`命令从表中删除。假设你对Hayward的天气不再感兴趣，那么你可以用下面的方法把那些行从表中删除：

```
DELETE FROM weather WHERE city = 'Hayward';
```

所有属于Hayward的天气记录都被删除。

```
SELECT * FROM weather;
```



```
     city      | temp_lo | temp_hi | prcp |    date
---------------+---------+---------+------+------------
 San Francisco |      46 |      50 | 0.25 | 1994-11-27
 San Francisco |      41 |      55 |    0 | 1994-11-29
(2 rows)
```



我们用下面形式的语句的时候一定要小心

```
DELETE FROM tablename;
```

如果没有一个限制，`DELETE`将从指定表中删除所有行，把它清空。做这些之前系统不会请求你确认！