---
title: 简单版
description: 简单版
published: true
date: 2021-08-23T03:56:35.627Z
tags: 
editor: markdown
dateCreated: 2020-12-20T05:50:08.904Z
---

## 16.1. 简单版



```
./configure
make
su
make install
adduser postgres
mkdir /usr/local/pgsql/data
chown postgres /usr/local/pgsql/data
su - postgres
/usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
/usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
/usr/local/pgsql/bin/createdb test
/usr/local/pgsql/bin/psql test
```

本章剩余部分都是完全版。