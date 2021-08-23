---
title: 继承
description: 继承
published: true
date: 2021-08-23T03:55:23.231Z
tags: 继承
editor: markdown
dateCreated: 2020-10-26T15:22:28.651Z
---

## 3.6. 继承



继承是面向对象数据库中的概念。它展示了数据库设计的新的可能性。

让我们创建两个表：表`cities`和表`capitals`。自然地，首都也是城市，所以我们需要有某种方式能够在列举所有城市的时候也隐式地包含首都。如果真的聪明，我们会设计如下的模式：

```
CREATE TABLE capitals (
  name       text,
  population real,
  altitude   int,    -- (in ft)
  state      char(2)
);

CREATE TABLE non_capitals (
  name       text,
  population real,
  altitude   int     -- (in ft)
);

CREATE VIEW cities AS
  SELECT name, population, altitude FROM capitals
    UNION
  SELECT name, population, altitude FROM non_capitals;
```

这个模式对于查询而言工作正常，但是当我们需要更新一些行时它就变得不好用了。

更好的方案是：

```
CREATE TABLE cities (
  name       text,
  population real,
  altitude   int     -- (in ft)
);

CREATE TABLE capitals (
  state      char(2)
) INHERITS (cities);
```



在这种情况下，一个`capitals`的行从它的*父亲*`cities`*继承*了所有列（`name`、`population`和`altitude`）。列`name`的类型是`text`，一种用于变长字符串的本地PostgreSQL类型。州首都有一个附加列`state`用于显示它们的州。在PostgreSQL中，一个表可以从0个或者多个表继承。

例如，如下查询可以寻找所有海拔500尺以上的城市名称，包括州首都：

```
SELECT name, altitude
  FROM cities
  WHERE altitude > 500;
```

它的返回为：

```
   name    | altitude
-----------+----------
 Las Vegas |     2174
 Mariposa  |     1953
 Madison   |      845
(3 rows)
```



在另一方面，下面的查询可以查找所有海拔高于500尺且不是州首府的城市：

```
SELECT name, altitude
    FROM ONLY cities
    WHERE altitude > 500;
```



```
   name    | altitude
-----------+----------
 Las Vegas |     2174
 Mariposa  |     1953
(2 rows)
```



其中`cities`之前的`ONLY`用于指示查询只在`cities`表上进行而不会涉及到继承层次中位于`cities`之下的其他表。很多我们已经讨论过的命令 — `SELECT`、`UPDATE` 和`DELETE` — 都支持这个`ONLY`记号。

### 注意

尽管继承很有用，但是它还未与唯一约束或外键集成，这也限制了它的可用性。更多详情见[第 5.10 节](ddl-inherit)。