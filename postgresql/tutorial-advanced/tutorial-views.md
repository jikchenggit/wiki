---
title: 视图
description: 视图
published: true
date: 2021-08-23T03:54:44.052Z
tags: 视图
editor: markdown
dateCreated: 2020-10-26T15:19:42.097Z
---

## 3.2. 视图



回想一下[第 2.6 节](tutorial-join)中的查询。假设天气记录和城市位置的组合列表对我们的应用有用，但我们又不想每次需要使用它时都敲入整个查询。我们可以在该查询上创建一个*视图*，这会给该查询一个名字，我们可以像使用一个普通表一样来使用它：

```
CREATE VIEW myview AS
    SELECT city, temp_lo, temp_hi, prcp, date, location
        FROM weather, cities
        WHERE city = name;

SELECT * FROM myview;
```



对视图的使用是成就一个好的SQL数据库设计的关键方面。视图允许用户通过始终如一的接口封装表的结构细节，这样可以避免表结构随着应用的进化而改变。

视图几乎可以用在任何可以使用表的地方。在其他视图基础上创建视图也并不少见。