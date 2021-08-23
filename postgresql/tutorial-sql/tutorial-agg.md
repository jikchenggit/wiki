---
title: 聚集函数
description: 聚集函数
published: true
date: 2021-08-23T03:54:02.107Z
tags: 聚集函数
editor: markdown
dateCreated: 2020-10-26T15:12:38.043Z
---

## 2.7. 聚集函数



和大多数其它关系数据库产品一样，PostgreSQL支持*聚集函数*。 一个聚集函数从多个输入行中计算出一个结果。 比如，我们有在一个行集合上计算`count`（计数）、`sum`（和）、`avg`（均值）、`max`（最大值）和`min`（最小值）的函数。

比如，我们可以用下面的语句找出所有记录中最低温度中的最高温度：

```
SELECT max(temp_lo) FROM weather;
```



```
 max
-----
  46
(1 row)
```



如果我们想知道该读数发生在哪个城市，我们可以用：

```
SELECT city FROM weather WHERE temp_lo = max(temp_lo);     错误
```

不过这个方法不能运转，因为聚集`max`不能被用于`WHERE`子句中（存在这个限制是因为`WHERE`子句决定哪些行可以被聚集计算包括；因此显然它必需在聚集函数之前被计算）。 不过，我们通常都可以用其它方法实现我们的目的；这里我们就可以使用*子查询*：

```
SELECT city FROM weather
    WHERE temp_lo = (SELECT max(temp_lo) FROM weather);
```



```
     city
---------------
 San Francisco
(1 row)
```

这样做是 OK 的，因为子查询是一次独立的计算，它独立于外层的查询计算出自己的聚集。

聚集同样也常用于和`GROUP BY`子句组合。比如，我们可以获取每个城市观测到的最低温度的最高值：

```
SELECT city, max(temp_lo)
    FROM weather
    GROUP BY city;
```



```
     city      | max
---------------+-----
 Hayward       |  37
 San Francisco |  46
(2 rows)
```

这样给我们每个城市一个输出。每个聚集结果都是在匹配该城市的表行上面计算的。我们可以用`HAVING` 过滤这些被分组的行：

```
SELECT city, max(temp_lo)
    FROM weather
    GROUP BY city
    HAVING max(temp_lo) < 40;
```



```
  city   | max
---------+-----
 Hayward |  37
(1 row)
```

这样就只给出那些所有`temp_lo`值曾都低于 40的城市。最后，如果我们只关心那些名字以“`S`”开头的城市，我们可以用：

```
SELECT city, max(temp_lo)
    FROM weather
    WHERE city LIKE 'S%'            -- (1)
    GROUP BY city
    HAVING max(temp_lo) < 40;
```



| [(1)](#co.tutorial-agg-like) | `LIKE`操作符进行模式匹配，在[第 9.7 节](functions-matching)里有解释。 |
| ---------------------------- | ------------------------------------------------------------ |
|                              |                                                              |



理解聚集和SQL的`WHERE`以及`HAVING`子句之间的关系对我们非常重要。`WHERE`和`HAVING`的基本区别如下：`WHERE`在分组和聚集计算之前选取输入行（因此，它控制哪些行进入聚集计算）， 而`HAVING`在分组和聚集之后选取分组行。因此，`WHERE`子句不能包含聚集函数； 因为试图用聚集函数判断哪些行应输入给聚集运算是没有意义的。相反，`HAVING`子句总是包含聚集函数（严格说来，你可以写不使用聚集的`HAVING`子句， 但这样做很少有用。同样的条件用在`WHERE`阶段会更有效）。

在前面的例子里，我们可以在`WHERE`里应用城市名称限制，因为它不需要聚集。这样比放在`HAVING`里更加高效，因为可以避免那些未通过 `WHERE`检查的行参与到分组和聚集计算中。