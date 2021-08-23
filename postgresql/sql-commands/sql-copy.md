---
title: sql-copy 命令
description: sql-copy 命令
published: true
date: 2021-08-23T03:56:06.986Z
tags: 
editor: markdown
dateCreated: 2020-12-17T13:43:20.529Z
---

# COPY 实战
## 规范CSV 文件
```bash
echo -n "Enter your want to conver file path: "
read file_name
# 删除空行
sed -i  '/^[  ]*$/d' $file_name
# awk 进行语句拼接.
awk -F , 'NR == 1''{split(FILENAME,a,"[.]") ;print " create table " a[1] " ( "}''NR == 1''{for(i=1;i<=NF;i++){if (i != NF) {print $i " character varying(255),"}  else {print $i " character varying(255));"} }}' $file_name
```
说明: 数据文件的绝对路径,输出建表语句. 

## 使用NULL 和FORCE NULL
```sql
\copy fipo_t_app_data  from FIPO_T_APP_DATA.csv CSV  NULL 'NULL' FORCE  NULL passport_expiry_date  WHERE ID_NO='29263404288' ;
```
说明: 从CSV 文件中导入数据. 且CSV 中为NULL 的字符串,强制为空. 
## 导入数据默认第一行为行头,不进行导入.
```sql
\copy fipo_t_app_data  from FIPO_T_APP_DATA.csv  WITH CSV  HEADER
```
## 过滤导入条件
```sql
\copy fipo_t_app_data  from FIPO_T_APP_DATA.csv  WITH CSV WHERE APPL_DATE='18-JAN-10';
```

## 指定特殊分隔符导入
```bash
postgres=# select ascii(E'\t');  
 ascii   
-------  
     9  
(1 row)  
```
执行导入操作.指定特殊的分割码.
```bash
postgres=# copy aa from '/home/digoal/aa.csv' with (delimiter U&'\0009');  
COPY 10  
  
postgres=# copy aa from '/home/digoal/aa.csv' with (delimiter E'\t');  
COPY 10 
```

## 指定分隔符导出
```bash
\copy fipo_t_app_data  to qnb/FIPO_T_APP_DATA_TO.CSV DELIMITER '|' ;
```

## 获取网站输出
```bash
$ CREATE TABLE words (id int4 PRIMARY KEY, word text);
CREATE TABLE
 
$ \copy words FROM program 'wget -q -O - https://www.depesz.com/various/words.csv'
 
$ SELECT COUNT(*) FROM words;
 COUNT
-------
 72188
(1 ROW)
```
# COPY

COPY — 在一个文件和一个表之间复制数据

## 大纲

```
COPY table_name [ ( column_name [, ...] ) ]
    FROM { 'filename' | PROGRAM 'command' | STDIN }
    [ [ WITH ] ( option [, ...] ) ]
    [ WHERE condition ]

COPY { table_name [ ( column_name [, ...] ) ] | ( query ) }
    TO { 'filename' | PROGRAM 'command' | STDOUT }
    [ [ WITH ] ( option [, ...] ) ]

其中 option 可以是下列之一：

    FORMAT format_name
    FREEZE [ boolean ]
    DELIMITER 'delimiter_character'
    NULL 'null_string'
    HEADER [ boolean ]
    QUOTE 'quote_character'
    ESCAPE 'escape_character'
    FORCE_QUOTE { ( column_name [, ...] ) | * }
    FORCE_NOT_NULL ( column_name [, ...] )
    FORCE_NULL ( column_name [, ...] )
    ENCODING 'encoding_name'
```

## 描述

`COPY`在 PostgreSQL表和标准文件系统文件之间 移动数据。`COPY TO`把一个表的内容复制 ***到\***一个文件，而`COPY FROM` 则***从\***一个文件复制数据到一个表（把数据追加到表中原有数 据）。`COPY TO`也能复制一个 `SELECT`查询的结果。

如果指定了一个列列表，`COPY TO`将只把指定列的数据复制到文件。对于`COPY FROM`，文件中的每个字段将按顺序插入到指定列中。`COPY FROM`命令的列列表中没有指定的表列则会采纳其默认值。

带一个文件名的`COPY`指示 PostgreSQL服务器直接从一个文件读取 或者写入到一个文件。该文件必须是 PostgreSQL用户（运行服务器的用户 ID） 可访问的并且应该以服务器的视角来指定其名称。当指定了 `PROGRAM`时，服务器执行给定的命令并且从该程序的标准 输出读取或者写入到该程序的标准输入。该程序必须以服务器的视角指定，并且 必须是PostgreSQL用户可执行的。在指定 `STDIN`或者`STDOUT`时，数据会通过客 户端和服务器之间的连接传输。

## 参数

- *`table_name`*

  一个现有表的名称（可以是模式限定的）。

- *`column_name`*

  可选的要被复制的列列表。如果没有指定列列表，则该表的所有列除了生成的列都会被复制。

- *`query`*

  其结果要被复制的[SELECT](http://postgres.cn/docs/12/sql-select.html)、 [VALUES](http://postgres.cn/docs/12/sql-values.html)、 [INSERT](http://postgres.cn/docs/12/sql-insert.html)、[UPDATE](http://postgres.cn/docs/12/sql-update.html)或者 [DELETE](http://postgres.cn/docs/12/sql-delete.html)命令。注意查询周围的圆括号是必要的。对于`INSERT`、`UPDATE`以及 `DELETE`查询，必须提供一个 RETURNING 子句并且 目标关系不能具有会扩展成多条语句的条件规则、 `ALSO`规则或者`INSTEAD`规则。

- *`filename`*

  输入或者输出文件的路径名。一个输入文件的名称可以是一个绝对或相对路径， 但一个输出文件的名称必须是绝对路径。Windows 用户可能需要使用一个 `E''`字符串并且双写路径名称中使用的任何反斜线。

- `PROGRAM`

  一个要执行的命令。在`COPY FROM`中，输入 将从该命令的标准输出读取，而在`COPY TO`中，输出会 写入到该命令的标准输入。注意该命令是由 shell 调用，因此如果你需要传递任何来自不可信来源的 参数给 shell 命令，你必须小心地剥离那些可能对 shell 有特殊意义的特殊 字符。出于安全原因，最好使用一个固定的命令字符串，或者至少避免传递 任何用户输入到其中。

- `STDIN`

  指定输入来自客户端应用。

- `STDOUT`

  指定输出会去到客户端应用。

- *`boolean`*

  指定选中的选项是应该被关闭还是打开。可以写`TRUE`、 `ON`或`1`来启用选项，写 `FALSE`、`OFF`或`0`禁用它。 *`boolean`*值也可以被省略， 那样会假定为`TRUE`。

- `FORMAT`

  选择要读取或者写入的数据格式： `text`、 `csv`（逗号分隔值）或者`binary`。 默认是`text`。

- `FREEZE`

  请求复制已经完成了行冻结的数据，就好像在运行 `VACUUM FREEZE`命令之后复制。这是为了初始 数据载入的性能而设计的。只有被载入表已经在当前子事务中被创建 或截断、该事务中没有游标打开并且该事务没有持有更旧的快照时， 行才会被冻结。目前无法在分区表上执行`COPY FREEZE`。注意一旦成功地载入，所有其他会话将能立即看到该数据。这违背了 普通的 MVCC 可见性规则，指定该选项的用户应该注意这可能会导致 的潜在问题。

- `DELIMITER`

  指定分隔文件每行中各列的字符。文本格式中默认是一个制表符， 而`CSV`格式中默认是一个逗号。这必须是一个单一 的单字节字符。使用`binary`格式时不允许这个选项。

- `NULL`

  指定表示一个空值的字符串。文本格式中默认是 `\N`（反斜线-N），`CSV`格式中默认 是一个未加引用的空串。在你不想区分空值和空串的情况下，即使在文本 格式中你也可能更喜欢空串。使用`binary`格式时不允许这 个选项。注意在使用`COPY FROM`时，任何匹配 这个串的数据项将被存储为空值，因此你应该确定你使用的是和 `COPY TO`时相同的串。

- `HEADER`

  指定文件包含标题行，其中有每一列的名称。在输出时，第一行包含 来自表的列名。在输入时，第一行会被忽略。只有使用 `CSV`格式时才允许这个选项。

- `QUOTE`

  指定一个数据值被引用时使用的引用字符。默认是双引号。 这必须是一个单一的单字节字符。只有使用 `CSV`格式时才允许这个选项。

- `ESCAPE`

  指定应该出现在一个匹配`QUOTE`值的数据字符之前 的字符。默认和`QUOTE`值一样（这样如果引用字符 出现在数据中，它会被双写）。这必须是一个单一的单字节字符。 只有使用`CSV`格式时才允许这个选项。

- `FORCE_QUOTE`

  强制必须对每个指定列中的所有非`NULL`值使用引用。 `NULL`输出不会被引用。如果指定了`*`， 所有列的非`NULL`值都将被引用。只有在 `COPY TO`中使用`CSV`格式时才允许 这个选项。

- `FORCE_NOT_NULL`

  不要把指定列的值与空值串匹配。在空值串就是空串的默认情况下， 这意味着空串将被读作长度为零的字符串而不是空值（即使它们没有 被引用）。只有在`COPY FROM`中使用 `CSV`格式时才允许这个选项。

- `FORCE_NULL`

  将指定列的值与空值串匹配（即使它已经被加上引号），并且在找到 匹配时将该值设置为`NULL`。在空值串就是空串的默认 情况下，这会把一个被引用的空串转换为 NULL。 只有在`COPY FROM`中使用 `CSV`格式时才允许这个选项。

- `ENCODING`

  指定文件被以*`encoding_name`*编码。如果省略 这个选项，将使用当前的客户端编码。详见下文的注解。

- `WHERE`

  `WHERE`子句是可选的，其一般形式是：`WHERE *`condition`* `其中*`condition`*是计算结果为`boolean`类型的任意表达式。任何不满足此条件的行都不会插入到表中。在用实际的行值替换任何变量引用时，如果该行返回true，则该行满足条件。目前，在`WHERE`表达式中不允许使用子查询，并且值的计算不会看到`COPY`本身所做的任何更改（当表达式包含对`VOLATILE`函数的调用时，这一点很重要）。

## 输出

在成功完成时，一个`COPY`命令会返回一个形为

```
COPY count
```

的命令标签。 *`count`*是被复制 的行数。

### 注意

如果命令不是`COPY ... TO STDOUT`或者等效的 psql元命令`\copy ... to stdout`， psql将只打印这个命令标签。这是为了防止弄混 命令标签和刚刚打印的数据。

## 注解

`COPY TO`只能被用于纯粹的表，不能用于视图。 不过你可以写`COPY (SELECT * FROM *`viewname`*) TO ...`来拷贝一个视图的当前内容。

`COPY FROM`可以被用于普通表、外部表、分区表或者具有`INSTEAD OF INSERT`触发器的视图。

`COPY`只处理提到的表，它不会从子表复制 数据或者复制数据到子表中。例如 `COPY *`table`* TO` 会显示与`SELECT * FROM ONLY *`table`*`相同的数据。而`COPY (SELECT * FROM *`table`*) TO ...` 可以被用来转储一个继承层次中的所有数据。

你必须拥有被`COPY TO`读取的表上的选择特权， 以及被`COPY FROM`插入的表上的插入特权。 拥有在命令中列出的列上的特权就足够了。

如果对表启用了行级安全性，相关的`SELECT`策略将应用于`COPY *`table`* TO`语句。当前，有行级安全性的表不支持`COPY FROM`。不过可以使用等效的`INSERT`语句。

`COPY`命令中提到的文件会被服务器（而不是 客户端应用）直接读取或写入。因此它们必须位于数据库服务器（不是客户 端）的机器上或者是数据库服务器可以访问的。它们必须是 PostgreSQL用户（运行服务器的用户 ID）可访问的并且是可读或者可写的。类似地，用`PROGRAM` 指定的命令也会由服务器（不是客户端应用）直接执行，它也必须是 PostgreSQL用户可以执行的。 只允许数据库超级用户或者授予了默认角色`pg_read_server_files`、`pg_write_server_files`及`pg_execute_server_program`之一的用户`COPY`一个文件或者命令， 因为它允许读取或者写入服务器有特权访问的任何文件或者运行服务器有特权访问的程序。

不要把`COPY`和 psql指令 `\copy` 弄混。`\copy`会调用 `COPY FROM STDIN`或者`COPY TO STDOUT`，然后读取/存储一个 psql客户端可访问的文件中的数据。 因此，在使用`\copy`时，文件的可访 问性和访问权利取决于客户端而不是服务器。

我们推荐在`COPY`中使用的文件名总是 指定为一个绝对路径。在`COPY TO`的 情况下服务器会强制这一点，但是对于 `COPY FROM`你可以选择从一个用相对 路径指定的文件中读取。该路径将根据服务器进程（而不是客户端） 的工作目录（通常是集簇的数据目录）解释。

用`PROGRAM`执行一个命令可能会受到操作系统 的访问控制机制（如 SELinux）的限制。

`COPY FROM`将调用目标表上的任何触发器 和检查约束。但是它不会调用规则。

对于标识列，`COPY FROM`命令将总是写上输入数据中提供的列值，这和`INSERT`的选项`OVERRIDING SYSTEM VALUE`的行为一样。

`COPY`输入和输出受到 `DateStyle`的影响。为了确保到其他 可能使用非默认`DateStyle`设置的 PostgreSQL安装的可移植性，在使用 `COPY TO`前应该把 `DateStyle`设置为`ISO`。避免转储把 `IntervalStyle`设置为 `sql_standard`的数据也是一个好主意，因为负的区间值可能会 被具有不同`IntervalStyle`设置的服务器解释错误。

即使数据会被服务器直接从一个文件读取或者写入一个文件而不通过 客户端，输入数据也会被根据`ENCODING`选项或者当前 客户端编码解释，并且输出数据会被根据`ENCODING`或 者当前客户端编码进行编码。

`COPY`会在第一个错误处停止操作。这在 `COPY TO`的情况下不会导致问题，但是 在`COPY FROM`中目标表将已经收到了一 些行。这些行将不会变得可见或者可访问，但是它们仍然占据磁盘空间。 如果在一次大型的复制操作中出现错误，这可能浪费相当可观的磁盘空间。 你可能希望调用`VACUUM`来恢复被浪费的 空间。

`FORCE_NULL`和`FORCE_NOT_NULL`可以被同时 用在同一列上。这会导致把已被引用的空值串转换为空值并且把未引用的空值 串转换为空串。

## 文件格式

### 文本格式

在使用`text`格式时，读取或写入的是一个文本文件， 其中每一行就是表中的一行。一行中的列被定界字符分隔。列值 本身是由输出函数产生的或者是可被输入函数接受的属于每个属性 数据类型的字符串。在为空值的列的位置使用指定的空值串。如果 输入文件的任何行包含比预期更多或者更少的列， `COPY FROM`将会抛出一个错误。

数据的结束可以表示为一个只包含反斜线-点号（`\.`）的 单一行。从一个文件读取时，数据结束标记并不是必要的，因为文件 结束符就已经足够用了。只有使用 3.0 客户端协议之前的客户端应用 复制数据时才需要它。

反斜线字符（`\`）可以被用在 `COPY`数据中来引用被用作行或者列定界符的 字符。特别地，如果下列字符作为一个列值的一部分出现，它们 ***必须\***被前置一个反斜线：反斜线本身、新行、回车以及 当前的定界符字符。

`COPY TO`会不加任何反斜线返回指定的空值串。 相反，`COPY FROM`会在移除反斜线之前把输入 与空值串相匹配。因此，一个空值串（例如`\N`）不会与实 际的数据值`\N`（它会被表示为`\\N`）搞混。

`COPY FROM`识别下列特殊的反斜线序列：

| 序列           | 表示                                                      |
| -------------- | --------------------------------------------------------- |
| `\b`           | 退格 (ASCII 8)                                            |
| `\f`           | 换页 (ASCII 12)                                           |
| `\n`           | 新行 (ASCII 10)                                           |
| `\r`           | 回车 (ASCII 13)                                           |
| `\t`           | 制表 (ASCII 9)                                            |
| `\v`           | 纵向制表 (ASCII 11)                                       |
| `\`*`digits`*  | 反斜线后跟一到三个十进制位表示该数字代码对应的字符        |
| `\x`*`digits`* | 反斜线加`x`后跟一到三个十六进制位表示该数字代码对应的字符 |

当前，`COPY TO`不会发出一个十进制或十六进制位 反斜线序列，但是它确实把上面列出的其他序列用于那些控制字符。

任何上述表格中没有提到的其他反斜线字符将被当作表示其本身。不过，要注意 增加不必要的反斜线，因为那可能意外地产生一个匹配数据结束标记（ `\.`）或者空值串（默认是`\N`）的字符串。这些字符串 将在完成任何其他反斜线处理之前被识别。

强烈建议产生`COPY`数据的应用把数据新行和回车分别 转换为`\n`和`\r`序列。当前可以把一个数据回车表示为 一个反斜线和回车，把一个数据新行表示为一个反斜线和新行。不过，未来的发行 可能不会接受这些表示。如果在不同的机器之间（例如从 Unix 到 Windows） 传输`COPY`文件，它们也很容易受到破坏。

`COPY TO`将用一个 Unix 风格的新行（ “`\n`”）终止每一行。运行在 Microsoft Windows 上的服务器则会输出回车/新行（“`\r\n`”），不过只对 `COPY`到一个服务器文件这样做。为了做到跨平台一致， `COPY TO STDOUT`总是发送“`\n`”而 不管服务器平台是什么。`COPY FROM`能够处理以 新行、回车或者回车/新行结尾的行。为了减少由作为数据的未加反斜线的新行 或者回车带来的风险，如果输出中的行结束并不完全相似， `COPY FROM`将会抱怨。

### CSV 格式

这种格式选项被用于导入和导出很多其他程序（例如电子表格）使用的逗号 分隔值（`CSV`）文件格式。不同于 PostgreSQL标准文本格式使用的转义 规则，它产生并且识别一般的 CSV 转义机制。

每个记录中的值用`DELIMITER`字符分隔。如果值包含 定界符字符、`QUOTE`字符、`NULL`字符串、 一个回车或者换行字符，那么整个值会被加上`QUOTE`字符 作为前缀或者后缀，并且在该值内`QUOTE`字符或者 `ESCAPE`字符的任何一次出现之前放上转义字符。在输出 指定列中非`NULL`值时，还可以使用 `FORCE_QUOTE`来强制加上引用。

`CSV`格式没有标准方式来区分`NULL`值和空字符串。 PostgreSQL的`COPY`用引用来处理 这种区分工作。`NULL`被按照`NULL`参数字符串输出 并且不会被引用，而匹配`NULL`参数字符串的非`NULL` 值会被加上引用。例如，使用默认设置时，`NULL`被写作一个未 被引用的空字符串，而一个空字符串数据值会被写成带双引号（`""`）。 值的读取遵循类似的规则。你可以用`FORCE_NOT_NULL`来防止 对指定列的`NULL`输入比较。你还可以使用 `FORCE_NULL`把带引用的空值字符串数据值转换成`NULL`。

因为反斜线在`CSV`格式中不是一种特殊字符，数据结束标记 `\.`也可以作为一个数据值出现。为了避免任何解释误会，在 一行上作为孤项出现的`\.`数据值输出时会自动被引用，并且 输入时如果被引用，则不会被解释为数据结束标记。如果正在载入一个由 另一个应用创建的文件并且其中具有一个未被引用的列且可能具有 `\.`值，你可能需要在输入文件中引用该值。

### 注意

`CSV`格式中，所有字符都是有意义的。一个被空白或者其他 非 `DELIMITER`字符围绕的引用值将包括那些字符。在导入 来自用空白填充`CSV`行到固定长度的系统的数据时，这可能 会导致错误。如果出现这种情况，在导入数据到 PostgreSQL之前，你可能需要预处理该 `CSV`文件以移除拖尾的空白。

### 注意

CSV 格式将识别并且产生带有包含嵌入的回车和换行的引用值的 CSV 文件。因此文件并不限于文本格式文件的每个表行一行的形式。

### 注意

很多程序会产生奇怪的甚至偶尔是不合常理的 CSV 文件，因此该文件 格式更像是一种习惯而不是标准。因此你可能会碰到一些无法使用这种 机制导入的文件，并且`COPY`也可能产生其他程序无 法处理的文件。

### 二进制格式

`binary`格式选项导致所有数据被以二进制格式 而不是文本格式存储/读取。它比文本和`CSV`格式要 快一些，但是二进制格式文件在不同的机器架构和 PostgreSQL版本之间的可移 植性要差些。还有，二进制格式与数据格式非常相关。例如不能从 一个`smallint`列中输出二进制数据并且把它读入到一个 `integer`列中，虽然这样做在文本格式中是可行的。

`binary`文件格式由一个文件头、零个或者更多个包含 行数据的元组以及一个文件尾构成。头部和数据都以网络字节序表示。

### 注意

7.4 之前的PostgreSQL发行 使用一种不同的二进制文件格式。

#### 文件头

文件头由 15 字节的固定域构成，后面跟着一个变长的头部扩展区。 固定域有：

- 签名

  11-字节的序列`PGCOPY\n\377\r\n\0` — 注意 零字节是签名的一个必要的部分（该签名是为了能容易地发现文件被 无法正确处理 8 位字符编码的传输所破坏。这个签名将被行尾翻译过 滤器、删除零字节、删除高位或者奇偶修改等改变）。

- 标志域

  32-位整数位掩码，用以表示该文件格式的重要方面。位被编号为 从 0 （LSB）到 31（MSB）。 注意这个域以网络字节序存放（最高有效位在前），所有该文件格式 中使用的整数域都是这样。16-31 位被保留用来表示严重的文件格式 问题， 读取者如果在这个范围内发现预期之外的被设置位，它应该 中止。0-15 位被保留用来表示向后兼容的格式问题，读取者应该简单 地略过这个范围内任何预期之外的被设置位。当前只定义了一个标志 位，其他位必须为零：位 16如果为 1，表示数据中包含 OID；如果为 0，则不包含。PostgreSQL不再支持Oid系统列，但是格式仍然包含该指示符。

- 头部扩展区长度

  32-为整数，表示头部剩余部分的以字节计的长度，不包括其本身。 当前，这个长度为零，并且其后就紧跟着第一个元组。未来对该 格式的更改可能会允许在头部中表示额外的数据。如果读取者不知 道要对头部扩展区数据做什么，可以安静地跳过它。



头部扩展区域被预期包含一个能自我解释的块的序列。 该标志域并不想告诉读取者扩展数据是什么。详细的 头部扩展内容的设计留给后来的发行去做。

这种设计允许向后兼容的头部增加（增加头部扩展块或者设置低位标志位）以及 非向后兼容的更改（设置高位标志位来表示这类更改并且在需要时向扩展区域 中增加支持数据）。

#### 元组

每一个元组由一个表示元组中域数量的 16 位整数计数开始（当前，一个表中 的所有元组都应该具有相同的计数，但是这可能不会总是为真）。然后是元组 中的每一个域，它是一个 32 位的长度字，后面则跟随着这么多个字节的域数 据（长度字不包括其本身，并且可以是零）。作为一种特殊情况，-1 表示一个 NULL 域值。在 NULL 情况下，后面不会跟随值字节。

在域之间没有对齐填充或者任何其他额外的数据。

当前，一个二进制格式文件中的所有数据值都被假设为二进制格式（格式代码一）。 可以预见未来的扩展可能会增加一个允许独立指定各列的格式代码的头部域。

要为实际的元组数据决定合适的二进制格式，你应该参考 PostgreSQL源码，特别是用于各列 数据类型的`*send`和`*recv`函数（通常可 以在源码的`src/backend/utils/adt/`目录中找到 这些函数）。

如果文件中包含 OID，OID 域会紧跟在域计数字之后。它是一个普通域， 不过它没有被包含在域计数中。注意PostgreSQL当前版本不支持oid系统列。

#### 文件尾

文件位由一个包含 -1 的 16 位整数字组成。这很容易与一个 元组的域计数字区分开。

如果一个域计数字不是 -1 也不是期望的列数，读取者应该报告错误。 这提供了一种针对某种数据不同步的额外检查。

## 示例

下面的例子使用竖线（`|`）作为域定界符把一个表复制到客户端：

```
COPY country TO STDOUT (DELIMITER '|');
```



从一个文件中复制数据到`country`表中：

```
COPY country FROM '/usr1/proj/bray/sql/country_data';
```



只把名称以 'A' 开头的国家复制到一个文件中：

```
COPY (SELECT * FROM country WHERE country_name LIKE 'A%') TO '/usr1/proj/bray/sql/a_list_countries.copy';
```



要复制到一个压缩文件中，你可以用管道把输出导到一个外部压缩程序：

```
COPY country TO PROGRAM 'gzip > /usr1/proj/bray/sql/country_data.gz';
```



这里是一个适合于从`STDIN`复制到表中的数据：

```
AF      AFGHANISTAN
AL      ALBANIA
DZ      ALGERIA
ZM      ZAMBIA
ZW      ZIMBABWE
```

注意每一行上的空白实际是一个制表符。

下面是用二进制格式输出的相同数据。该数据是用 Unix 工具 `od -c`过滤后显示的。该表具有三列， 第一列类型是`char(2)`，第二列类型是`text`， 第三列类型是`integer`。所有行在第三列都是空值。

```
0000000   P   G   C   O   P   Y  \n 377  \r  \n  \0  \0  \0  \0  \0  \0
0000020  \0  \0  \0  \0 003  \0  \0  \0 002   A   F  \0  \0  \0 013   A
0000040   F   G   H   A   N   I   S   T   A   N 377 377 377 377  \0 003
0000060  \0  \0  \0 002   A   L  \0  \0  \0 007   A   L   B   A   N   I
0000100   A 377 377 377 377  \0 003  \0  \0  \0 002   D   Z  \0  \0  \0
0000120 007   A   L   G   E   R   I   A 377 377 377 377  \0 003  \0  \0
0000140  \0 002   Z   M  \0  \0  \0 006   Z   A   M   B   I   A 377 377
0000160 377 377  \0 003  \0  \0  \0 002   Z   W  \0  \0  \0  \b   Z   I
0000200   M   B   A   B   W   E 377 377 377 377 377 377
```

## 兼容性

SQL 标准中没有`COPY`语句。

下列语法用于PostgreSQL 9.0 之前的版本， 并且仍然被支持：

```
COPY table_name [ ( column_name [, ...] ) ]
    FROM { 'filename' | STDIN }
    [ [ WITH ]
          [ BINARY ]
          [ DELIMITER [ AS ] 'delimiter_character' ]
          [ NULL [ AS ] 'null_string' ]
          [ CSV [ HEADER ]
                [ QUOTE [ AS ] 'quote_character' ]
                [ ESCAPE [ AS ] 'escape_character' ]
                [ FORCE NOT NULL column_name [, ...] ] ] ]

COPY { table_name [ ( column_name [, ...] ) ] | ( query ) }
    TO { 'filename' | STDOUT }
    [ [ WITH ]
          [ BINARY ]
          [ DELIMITER [ AS ] 'delimiter_character' ]
          [ NULL [ AS ] 'null_string' ]
          [ CSV [ HEADER ]
                [ QUOTE [ AS ] 'quote_character' ]
                [ ESCAPE [ AS ] 'escape_character' ]
                [ FORCE QUOTE { column_name [, ...] | * } ] ] ]
```

注意在这种语法中，`BINARY`和`CSV`被视作独立的关键词， 而不是`FORMAT`选项的参数。

下列语法用于PostgreSQL 7.3 之前的版本， 并且仍然被支持：

```
COPY [ BINARY ] table_name
    FROM { 'filename' | STDIN }
    [ [USING] DELIMITERS 'delimiter_character' ]
    [ WITH NULL AS 'null_string' ]

COPY [ BINARY ] table_name
    TO { 'filename' | STDOUT }
    [ [USING] DELIMITERS 'delimiter_character' ]
    [ WITH NULL AS 'null_string' ]
```