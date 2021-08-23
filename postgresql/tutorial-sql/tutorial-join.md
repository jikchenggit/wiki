---
title: join
description: join
published: true
date: 2021-08-23T03:53:52.166Z
tags: join
editor: markdown
dateCreated: 2020-10-26T15:10:26.082Z
---

## 2.6. 在表之间连接



到目前为止，我们的查询一次只访问一个表。查询可以一次访问多个表，或者用这种方式访问一个表而同时处理该表的多个行。 一个同时访问同一个或者不同表的多个行的查询叫*连接*查询。举例来说，比如你想列出所有天气记录以及相关的城市位置。要实现这个目标，我们需要拿 `weather`表每行的`city`列和`cities`表所有行的`name`列进行比较， 并选取那些在该值上相匹配的行对。

### 注意

这里只是一个概念上的模型。连接通常以比实际比较每个可能的行对更高效的方式执行， 但这些是用户看不到的。

这个任务可以用下面的查询来实现：

```
SELECT *
    FROM weather, cities
    WHERE city = name;
```



```
     city      | temp_lo | temp_hi | prcp |    date    |     name      | location
---------------+---------+---------+------+------------+---------------+-----------
 San Francisco |      46 |      50 | 0.25 | 1994-11-27 | San Francisco | (-194,53)
 San Francisco |      43 |      57 |    0 | 1994-11-29 | San Francisco | (-194,53)
(2 rows)
```



观察结果集的两个方面：

- 没有城市Hayward的结果行。这是因为在`cities`表里面没有Hayward的匹配行，所以连接忽略 `weather`表里的不匹配行。我们稍后将看到如何修补它。

- 有两个列包含城市名字。这是正确的， 因为`weather`和`cities`表的列被串接在一起。不过，实际上我们不想要这些， 因此你将可能希望明确列出输出列而不是使用`*`：

  ```
  SELECT city, temp_lo, temp_hi, prcp, date, location
      FROM weather, cities
      WHERE city = name;
  ```

  



**练习：.** 看看这个查询省略`WHERE`子句的语义是什么

因为这些列的名字都不一样，所以规划器自动地找出它们属于哪个表。如果在两个表里有重名的列，你需要*限定*列名来说明你究竟想要哪一个，如：

```
SELECT weather.city, weather.temp_lo, weather.temp_hi,
       weather.prcp, weather.date, cities.location
    FROM weather, cities
    WHERE cities.name = weather.city;
```

人们广泛认为在一个连接查询中限定所有列名是一种好的风格，这样即使未来向其中一个表里添加重名列也不会导致查询失败。

到目前为止，这种类型的连接查询也可以用下面这样的形式写出来：

```
SELECT *
    FROM weather INNER JOIN cities ON (weather.city = cities.name);
```

这个语法并不象上文的那个那么常用，我们在这里写出来是为了让你更容易了解后面的主题。

现在我们将看看如何能把Hayward记录找回来。我们想让查询干的事是扫描`weather`表， 并且对每一行都找出匹配的`cities`表行。如果我们没有找到匹配的行，那么我们需要一些“空值”代替cities表的列。 这种类型的查询叫*外连接* （我们在此之前看到的连接都是内连接）。这样的命令看起来象这样：

```
SELECT *
    FROM weather LEFT OUTER JOIN cities ON (weather.city = cities.name);

     city      | temp_lo | temp_hi | prcp |    date    |     name      | location
---------------+---------+---------+------+------------+---------------+-----------
 Hayward       |      37 |      54 |      | 1994-11-29 |               |
 San Francisco |      46 |      50 | 0.25 | 1994-11-27 | San Francisco | (-194,53)
 San Francisco |      43 |      57 |    0 | 1994-11-29 | San Francisco | (-194,53)
(3 rows)
```

这个查询是一个*左外连接*， 因为在连接操作符左部的表中的行在输出中至少要出现一次， 而在右部的表的行只有在能找到匹配的左部表行时才被输出。 如果输出的左部表的行没有对应匹配的右部表的行，那么右部表行的列将填充空值（null）。

**练习：.** 还有右外连接和全外连接。试着找出来它们能干什么。

我们也可以把一个表和自己连接起来。这叫做*自连接*。 比如，假设我们想找出那些在其它天气记录的温度范围之外的天气记录。这样我们就需要拿 `weather`表里每行的`temp_lo`和`temp_hi`列与`weather`表里其它行的`temp_lo`和`temp_hi`列进行比较。我们可以用下面的查询实现这个目标：

```
SELECT W1.city, W1.temp_lo AS low, W1.temp_hi AS high,
    W2.city, W2.temp_lo AS low, W2.temp_hi AS high
    FROM weather W1, weather W2
    WHERE W1.temp_lo < W2.temp_lo
    AND W1.temp_hi > W2.temp_hi;

     city      | low | high |     city      | low | high
---------------+-----+------+---------------+-----+------
 San Francisco |  43 |   57 | San Francisco |  46 |   50
 Hayward       |  37 |   54 | San Francisco |  46 |   50
(2 rows)
```

在这里我们把weather表重新标记为`W1`和`W2`以区分连接的左部和右部。你还可以用这样的别名在其它查询里节约一些敲键，比如：

```
SELECT *
    FROM weather w, cities c
    WHERE w.city = c.name;
```

你以后会经常碰到这样的缩写的。