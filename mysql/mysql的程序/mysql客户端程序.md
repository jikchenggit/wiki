---
title: mysql客户端程序
description: mysql客户端程序
published: true
date: 2021-08-23T03:25:16.459Z
tags: mysql, 程序, 客户端
editor: markdown
dateCreated: 2020-03-17T13:50:45.487Z
---

# 1. MySQL 客户端程序

## 1.1. mysql 命令行工具.
mysql  是一个简单的SQL shell ,具有输入行编辑功能.它支持交互式和非交互式使用.

当交互使用时, 查询结果以ascii 表格显示.当非交互的时候 ,结果以制表符显示.可以使用命令行选项改变输出格式.

如果内存不足而不能处理大性结果集,请使用`--quick` 选项. 这个使得MySQL 每次从服务器检索一行结果进行显示.而不是结果集出现后缓冲到内存中.

使用的函数为`mysql_use_result()`  and `mysql_store_result().`

批量非交互:
```bash

mysql db_name < script.sql > output.tab
```

### 1.1.1. mysql 命令行选项

 - [`--auto-rehash`]
 启动自动处理.默认情况下这个参数打开的.使得 使用命令时的表明和列名自动完成. 该特灵需要readline 库.

- [`--auto-vertical-output`]
 
 结果集对于当前窗口太宽,导致结果集垂直显示,    使用这个选项则使用普通表格模式.
 
-  --batch, -B


使用制表符作为列分隔符的打印结果,每一行都在新的行上.如果使用这个选项,mysql 不适用历史记录文件.

-  --column-names

在输出结果中写入列明.

 - --connect-expired-password

如果账户过去,这个选项表明客户机可以处理沙箱模式.

- --default-character-set=charset_name
指定客户端字符集.

- [``--defaults-extra-file=*`file_name`*``]
    
    在读取全局文件之后读取此选项文件. 如果只给了文件名称.则在当前目录中找.
    
- [``--defaults-file=*`file_name`*``]
    
 只读取给定的选项文件.如果文件不存在或者无法访问,则会报错,如果只给了相对路径而不是完成路径,则在当前目录中找.

-  --html, -H

 生成 html 输出


 - --pager[=command]

输出结果分页


### 1.1.2. mysql 内部命令

- charset charset_name, \C charset_name

更改默认字符集,并设置当前会话为某种字符集.

- clear, \c

清除当前输入.

-  connect [db_name host_name]], \r [db_name host_name]]

重新连接到服务器. ,可以指定主机名数据库 ,如果省略则使用当前值.

- edit, \e

编辑当前的输入语句.使用vim .

- nopager \n
禁止使用分页.

- notee, \t

禁止输出文件.

- nowarning, \w

禁止显示语句后的警告.

- pager [command], \P [command]
使用操作系统的命令进行数据分页 (less,more)
 - 定制分页程序.
```sql
pager less -n -i -S -F -X
```
-  定制重定向输出和屏幕显示.
```sql
 pager cat | tee /dr1/tmp/res.txt \
          | tee /dr2/tmp/res2.txt | less -n -i -S
```

- print, \p

打印当前输入语句不执行.

- prompt [str], \R [str]

mysql> 提示符是否更改.
使用环境变量:
```sql

shell> export MYSQL_PS1="(\u@\h) [\d]> "
```
使用命令行
```sql
shell> mysql --prompt="(\u@\h) [\d]> "
```
使用配置文件
```sql

[mysql]
prompt=(\\u@\\h) [\\d]>\\_

[mysql]
prompt="\\r:\\m:\\s> "
```

使用内部命令
```sql
mysql> prompt (\u@\h) [\d]>\_
PROMPT set to '(\u@\h) [\d]>\_'
(user@host) [database]>
(user@host) [database]> prompt
Returning to default PROMPT of mysql>
mysql>
```

- rehash, \#

重新生成散列,用于自动补全功能.

- source file_name, \. file_name

读取文件并执行sql 语句.

- status, \s

连接的状态信息.

- system command, \! command

执行操作系统命令.

- tee [file_name], \T [file_name]
 
 通过调用tee 调用mysql 时使用 --tree 选项,可以记录语句及输出.屏幕上所显示的所有内容都会增加到一个给定的文件. 使用notee 和tee 开关管理tee 文件.再次执行tee 可以重新启用日志记录.

- use db_name, \u db_name

使用哪个数据库作为默认数据.

- warnings, \W

在每个语句之后显示警告信息.
