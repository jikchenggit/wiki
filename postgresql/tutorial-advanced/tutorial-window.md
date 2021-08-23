---
title: 窗口函数
description: 窗口函数
published: true
date: 2021-08-23T03:55:05.257Z
tags: 窗口函数
editor: markdown
dateCreated: 2020-10-26T15:21:39.342Z
---

## 3.5. 窗口函数



一个*窗口函数*在一系列与当前行有某种关联的表行上执行一种计算。这与一个聚集函数所完成的计算有可比之处。但是窗口函数并不会使多行被聚集成一个单独的输出行，这与通常的非窗口聚集函数不同。取而代之，行保留它们独立的标识。在这些现象背后，窗口函数可以访问的不仅仅是查询结果的当前行。

下面是一个例子用于展示如何将每一个员工的薪水与他/她所在部门的平均薪水进行比较：

```
SELECT depname, empno, salary, avg(salary) OVER (PARTITION BY depname) FROM empsalary;
```



```
  depname  | empno | salary |          avg
-----------+-------+--------+-----------------------
 develop   |    11 |   5200 | 5020.0000000000000000
 develop   |     7 |   4200 | 5020.0000000000000000
 develop   |     9 |   4500 | 5020.0000000000000000
 develop   |     8 |   6000 | 5020.0000000000000000
 develop   |    10 |   5200 | 5020.0000000000000000
 personnel |     5 |   3500 | 3700.0000000000000000
 personnel |     2 |   3900 | 3700.0000000000000000
 sales     |     3 |   4800 | 4866.6666666666666667
 sales     |     1 |   5000 | 4866.6666666666666667
 sales     |     4 |   4800 | 4866.6666666666666667
(10 rows)
```

最开始的三个输出列直接来自于表`empsalary`，并且表中每一行都有一个输出行。第四列表示对与当前行具有相同`depname`值的所有表行取得平均值（这实际和非窗口`avg`聚集函数是相同的函数，但是`OVER`子句使得它被当做一个窗口函数处理并在一个合适的窗口帧上计算。）。

一个窗口函数调用总是包含一个直接跟在窗口函数名及其参数之后的`OVER`子句。这使得它从句法上和一个普通函数或非窗口函数区分开来。`OVER`子句决定究竟查询中的哪些行被分离出来由窗口函数处理。`OVER`子句中的`PARTITION BY`子句指定了将具有相同`PARTITION BY`表达式值的行分到组或者分区。对于每一行，窗口函数都会在当前行同一分区的行上进行计算。

我们可以通过`OVER`上的`ORDER BY`控制窗口函数处理行的顺序（窗口的`ORDER BY`并不一定要符合行输出的顺序。）。下面是一个例子：

```
SELECT depname, empno, salary,
       rank() OVER (PARTITION BY depname ORDER BY salary DESC) FROM empsalary;
```



```
  depname  | empno | salary | rank
-----------+-------+--------+------
 develop   |     8 |   6000 |    1
 develop   |    10 |   5200 |    2
 develop   |    11 |   5200 |    2
 develop   |     9 |   4500 |    4
 develop   |     7 |   4200 |    5
 personnel |     2 |   3900 |    1
 personnel |     5 |   3500 |    2
 sales     |     1 |   5000 |    1
 sales     |     4 |   4800 |    2
 sales     |     3 |   4800 |    2
(10 rows)
```

如上所示，`rank`函数在当前行的分区内按照`ORDER BY`子句的顺序为每一个可区分的`ORDER BY`值产生了一个数字等级。`rank`不需要显式的参数，因为它的行为完全决定于`OVER`子句。

一个窗口函数所考虑的行属于那些通过查询的`FROM`子句产生并通过`WHERE`、`GROUP BY`、`HAVING`过滤的“虚拟表”。例如，一个由于不满足`WHERE`条件被删除的行是不会被任何窗口函数所见的。在一个查询中可以包含多个窗口函数，每个窗口函数都可以用不同的`OVER`子句来按不同方式划分数据，但是它们都作用在由虚拟表定义的同一个行集上。

我们已经看到如果行的顺序不重要时`ORDER BY`可以忽略。`PARTITION BY`同样也可以被忽略，在这种情况下会产生一个包含所有行的分区。

这里有一个与窗口函数相关的重要概念：对于每一行，在它的分区中的行集被称为它的窗口帧。 一些窗口函数只作用在*窗口帧*中的行上，而不是整个分区。默认情况下，如果使用`ORDER BY`，则帧包括从分区开始到当前行的所有行，以及后续任何与当前行在`ORDER BY`子句上相等的行。如果`ORDER BY`被忽略，则默认帧包含整个分区中所有的行。 [[4\]](#ftn.id-1.4.5.6.9.5) 下面是使用`sum`的例子：

```
SELECT salary, sum(salary) OVER () FROM empsalary;
 salary |  sum
--------+-------
   5200 | 47100
   5000 | 47100
   3500 | 47100
   4800 | 47100
   3900 | 47100
   4200 | 47100
   4500 | 47100
   4800 | 47100
   6000 | 47100
   5200 | 47100
(10 rows)
```

如上所示，由于在`OVER`子句中没有`ORDER BY`，窗口帧和分区一样，而如果缺少`PARTITION BY`则和整个表一样。换句话说，每个合计都会在整个表上进行，这样我们为每一个输出行得到的都是相同的结果。但是如果我们加上一个`ORDER BY`子句，我们会得到非常不同的结果：

```
SELECT salary, sum(salary) OVER (ORDER BY salary) FROM empsalary;
 salary |  sum
--------+-------
   3500 |  3500
   3900 |  7400
   4200 | 11600
   4500 | 16100
   4800 | 25700
   4800 | 25700
   5000 | 30700
   5200 | 41100
   5200 | 41100
   6000 | 47100
(10 rows)
```

这里的合计是从第一个（最低的）薪水一直到当前行，包括任何与当前行相同的行（注意相同薪水行的结果）。

窗口函数只允许出现在查询的`SELECT`列表和`ORDER BY`子句中。它们不允许出现在其他地方，例如`GROUP BY`、`HAVING`和`WHERE`子句中。这是因为窗口函数的执行逻辑是在处理完这些子句之后。另外，窗口函数在非窗口聚集函数之后执行。这意味着可以在窗口函数的参数中包括一个聚集函数，但反过来不行。

如果需要在窗口计算执行后进行过滤或者分组，我们可以使用子查询。例如：

```
SELECT depname, empno, salary, enroll_date
FROM
  (SELECT depname, empno, salary, enroll_date,
          rank() OVER (PARTITION BY depname ORDER BY salary DESC, empno) AS pos
     FROM empsalary
  ) AS ss
WHERE pos < 3;
```

上述查询仅仅显示了内层查询中`rank`低于3的结果。

当一个查询涉及到多个窗口函数时，可以将每一个分别写在一个独立的`OVER`子句中。但如果多个函数要求同一个窗口行为时，这种做法是冗余的而且容易出错的。替代方案是，每一个窗口行为可以被放在一个命名的`WINDOW`子句中，然后在`OVER`中引用它。例如：

```
SELECT sum(salary) OVER w, avg(salary) OVER w
  FROM empsalary
  WINDOW w AS (PARTITION BY depname ORDER BY salary DESC);
```



关于窗口函数的更多细节可以在[第 4.2.8 节](sql-expressions#SYNTAX-WINDOW-FUNCTIONS)、[第 9.21 节](functions-window)、[第 7.2.5 节](queries-table-expressions#QUERIES-WINDOW)以及[SELECT](sql-select)参考页中找到。



------

[[4\] ](#id-1.4.5.6.9.5)还有些选项用于以其他方式定义窗口帧，但是这不包括在本教程内。详见[第 4.2.8 节](sql-expressions#SYNTAX-WINDOW-FUNCTIONS)。