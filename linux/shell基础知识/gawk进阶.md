---
title: gawk进阶
description: gawk进阶
published: true
date: 2021-08-23T03:37:18.237Z
tags: gawk进阶
editor: markdown
dateCreated: 2020-07-18T15:56:01.801Z
---

[sed和gawk章节](/linux/shell基础知识/sed和gawk)前面介绍了gawk程序，并演示了用它从原始数据文件生成格式化报表的基本方法。本章将进一步深入了解如何定制gawk。gawk是一门功能丰富的编程语言，你可以通过它所提供的各种特性来编写高级程序处理数据。如果你在接触shell脚本前用过其他编程语言，那么gawk会让你感到十分亲切。在本章，你将会了解如何使用gawk编程语言来编写程序，处理可能遇到的各种数据格式化任务。

# 使用变量
所有编程语言共有的一个重要特性是使用变量来存取值。gawk编程语言支持两种不同类型的变量：

- 内建变量

- 自定义变量

gawk有一些内建变量。这些变量存放用来处理数据文件中的数据字段和记录的信息。你也可以在gawk程序里创建你自己的变量。下面几节将带你逐步了解如何在gawk程序里使用变量。

## 内建变量
gawk程序使用内建变量来引用程序数据里的一些特殊功能。本节将介绍gawk程序中可用的内建变量并演示如何使用它们。
**1. 字段和记录分隔符变量**
[sed和gawk章节](/linux/shell基础知识/sed和gawk)前面演示了gawk中的一种内建变量类型——数据字段变量。数据字段变量允许你使用美元符号（$）和字段在该记录中的位置值来引用记录对应的字段。因此，要引用记录中的第一个数据字段，就用变量$1；要引用第二个字段，就用$2，依次类推。

数据字段是由字段分隔符来划定的。默认情况下，字段分隔符是一个空白字符，也就是空格符或者制表符。第19章讲了如何在命令行下使用命令行参数-F或者在gawk程序中使用特殊的内建变量FS来更改字段分隔符。

内建变量FS是一组内建变量中的一个，这组变量用于控制gawk如何处理输入输出数据中的字段和记录。表22-1列出了这些内建变量
**gawk数据字段和记录变量**
|     变量      |                    描述                     |
| ------------- | ------------------------------------------- |
| `FIELDWIDTHS` | 由空格分隔的一列数字，定义了每个数据字段确切宽度 |
| `FS`          | 输入字段分隔符                               |
| `RS`          | 输入记录分隔符                               |
| `OFS`         | 输出字段分隔符                               |
| `ORS`         | 输出记录分隔符                               |


变量FS和OFS定义了gawk如何处理数据流中的数据字段。你已经知道了如何使用变量FS来定义记录中的字段分隔符。变量OFS具备相同的功能，只不过是用在print命令的输出上。

默认情况下，gawk将OFS设成一个空格，所以如果你用命令：
```bash
print $1,$2,$3
```
会看到如下输出：
```bash
field1 field2 field3
```
在下面的例子里，你能看到这点。
```bash
$ cat data1
data11,data12,data13,data14,data15
data21,data22,data23,data24,data25
data31,data32,data33,data34,data35
$ gawk 'BEGIN{FS=","} {print $1,$2,$3}' data1
data11 data12 data13
data21 data22 data23
data31 data32 data33
$

```
print命令会自动将OFS变量的值放置在输出中的每个字段间。通过设置OFS变量，可以在输出中使用任意字符串来分隔字段。
```bash
$ gawk 'BEGIN{FS=","; OFS="-"} {print $1,$2,$3}' data1
data11-data12-data13
data21-data22-data23
data31-data32-data33
$ gawk 'BEGIN{FS=","; OFS="--"} {print $1,$2,$3}' data1
data11--data12--data13
data21--data22--data23
data31--data32--data33
$ gawk 'BEGIN{FS=","; OFS="<-->"} {print $1,$2,$3}' data1
data11<-->data12<-->data13
data21<-->data22<-->data23
data31<-->data32<-->data33
$
```

FIELDWIDTHS变量允许你不依靠字段分隔符来读取记录。在一些应用程序中，数据并没有使用字段分隔符，而是被放置在了记录中的特定列。这种情况下，必须设定FIELDWIDTHS变量来匹配数据在记录中的位置。

一旦设置了FIELDWIDTH变量，gawk就会忽略FS变量，并根据提供的字段宽度来计算字段。下面是个采用字段宽度而非字段分隔符的例子。
```bash
$ cat data1b
1005.3247596.37
115-2.349194.00
05810.1298100.1
$ gawk 'BEGIN{FIELDWIDTHS="3 5 2 5"}{print $1,$2,$3,$4}' data1b
100 5.324 75 96.37
115 -2.34 91 94.00
058 10.12 98 100.1
$
```

FIELDWIDTHS变量定义了四个字段，gawk依此来解析数据记录。每个记录中的数字串会根据已定义好的字段长度来分割。

警告　一定要记住，一旦设定了FIELDWIDTHS变量的值，就不能再改变了。这种方法并不适用于变长的字段。

变量RS和ORS定义了gawk程序如何处理数据流中的字段。默认情况下，gawk将RS和ORS设为换行符。默认的RS值表明，输入数据流中的每行新文本就是一条新纪录。

有时，你会在数据流中碰到占据多行的字段。典型的例子是包含地址和电话号码的数据，其中地址和电话号码各占一行。
```bash
Riley Mullen
123 Main Street
Chicago, IL 60601
(312)555-1234
```
如果你用默认的FS和RS变量值来读取这组数据，gawk就会把每行作为一条单独的记录来读取，并将记录中的空格当作字段分隔符。这可不是你希望看到的。

要解决这个问题，只需把FS变量设置成换行符。这就表明数据流中的每行都是一个单独的字段，每行上的所有数据都属于同一个字段。但现在令你头疼的是无从判断一个新的数据行从何开始。

对于这一问题，可以把RS变量设置成空字符串，然后在数据记录间留一个空白行。gawk会把每个空白行当作一个记录分隔符。

下面的例子使用了这种方法。
```bash
$ cat data2
Riley Mullen
123 Main Street
Chicago, IL  60601
(312)555-1234

Frank Williams
456 Oak Street
Indianapolis, IN  46201
(317)555-9876

Haley Snell
4231 Elm Street
Detroit, MI 48201
(313)555-4938
$ gawk 'BEGIN{FS="\n"; RS=""} {print $1,$4}' data2
Riley Mullen (312)555-1234
Frank Williams (317)555-9876
Haley Snell (313)555-4938
$
```
太好了，现在gawk把文件中的每行都当成一个字段，把空白行当作记录分隔符。
**2. 数据变量**
除了字段和记录分隔符变量外，gawk还提供了其他一些内建变量来帮助你了解数据发生了什么变化，并提取shell环境的信息。表22-2列出了gawk中的其他内建变量。

|     变量     |                       描述                        |
| ------------ | ------------------------------------------------- |
| `ARGC`       | 当前命令行参数个数                                  |
| `ARGIND`     | 当前文件在`ARGV`中的位置                            |
| `ARGV`       | 包含命令行参数的数组                                |
| `CONVFMT`    | 数字的转换格式（参见`printf`语句），默认值为`%.6 g`   |
| `ENVIRON`    | 当前shell环境变量及其值组成的关联数组                |
| `ERRNO`      | 当读取或关闭输入文件发生错误时的系统错误号            |
| `FILENAME`   | 用作gawk输入数据的数据文件的文件名                   |
| `FNR`        | 当前数据文件中的数据行数                            |
| `IGNORECASE` | 设成非零值时，忽略`gawk`命令中出现的字符串的字符大小写 |
| `NF`         | 数据文件中的字段总数                                |
| `NR`         | 已处理的输入记录数                                  |
| `OFMT`       | 数字的输出格式，默认值为`%.6 g`                     |
| `RLENGTH`    | 由`match`函数所匹配的子字符串的长度                  |
| `RSTART`     | 由`match`函数所匹配的子字符串的起始位置              |

你应该能从上面的列表中认出一些shell脚本编程中的变量。ARGC和ARGV变量允许从shell中获得命令行参数的总数以及它们的值。但这可能有点麻烦，因为gawk并不会将程序脚本当成命令行参数的一部分。
```bash
$ gawk 'BEGIN{print ARGC,ARGV[1]}' data1
2 data1
$
```
ARGC变量表明命令行上有两个参数。这包括gawk命令和data1参数（记住，程序脚本并不算参数）。ARGV数组从索引0开始，代表的是命令。第一个数组值是gawk命令后的第一个命令行参数。

> 说明　跟shell变量不同，在脚本中引用gawk变量时，变量名前不加美元符。

ENVIRON变量看起来可能有点陌生。它使用关联数组来提取shell环境变量。关联数组用文本作为数组的索引值，而不是数值。

数组索引中的文本是shell环境变量名，而数组的值则是shell环境变量的值。下面有个例子。
```bash
$ gawk '
> BEGIN{
> print ENVIRON["HOME"]
> print ENVIRON["PATH"]
> }'
/home/rich
/usr/local/bin:/bin:/usr/bin:/usr/X11R6/bin
$
```
`ENVIRON["HOME"]`变量从shell中提取了HOME环境变量的值。类似地，`ENVIRON["PATH"]`提取了PATH环境变量的值。可以用这种方法来从shell中提取任何环境变量的值，以供gawk程序使用。

当要在gawk程序中跟踪数据字段和记录时，变量FNR、NF和NR用起来就非常方便。有时你并不知道记录中到底有多少个数据字段。NF变量可以让你在不知道具体位置的情况下指定记录中的最后一个数据字段。
```bash
$ gawk 'BEGIN{FS=":"; OFS=":"} {print $1,$NF}' /etc/passwd
rich:/bin/bash
testy:/bin/csh
mark:/bin/bash
dan:/bin/bash
mike:/bin/bash
test:/bin/bash
$

```
NF变量含有数据文件中最后一个数据字段的数字值。可以在它前面加个美元符将其用作字段变量。

FNR和NR变量虽然类似，但又略有不同。FNR变量含有当前数据文件中已处理过的记录数，NR变量则含有已处理过的记录总数。让我们看几个例子来了解一下这个差别。
```bash
$ gawk 'BEGIN{FS=","}{print $1,"FNR="FNR}' data1 data1
data11 FNR=1
data21 FNR=2
data31 FNR=3
data11 FNR=1
data21 FNR=2
data31 FNR=3
$

```
在这个例子中，gawk程序的命令行定义了两个输入文件（两次指定的是同样的输入文件）。这个脚本会打印第一个数据字段的值和FNR变量的当前值。注意，当gawk程序处理第二个数据文件时，FNR值被设回了1。

现在，让我们加上NR变量看看会输出什么。
```bash
$ gawk '
> BEGIN {FS=","}
> {print $1,"FNR="FNR,"NR="NR}
> END{print "There were",NR,"records processed"}' data1 data1
data11 FNR=1 NR=1
data21 FNR=2 NR=2
data31 FNR=3 NR=3
data11 FNR=1 NR=4
data21 FNR=2 NR=5
data31 FNR=3 NR=6
There were 6 records processed
$
```
FNR变量的值在gawk处理第二个数据文件时被重置了，而NR变量则在处理第二个数据文件时继续计数。结果就是：如果只使用一个数据文件作为输入， FNR和NR的值是相同的；如果使用多个数据文件作为输入， FNR的值会在处理每个数据文件时被重置，而NR的值则会继续计数直到处理完所有的数据文件。

> 说明　在使用gawk时你可能会注意到，gawk脚本通常会比shell脚本中的其他部分还要大一些。为了简单起见，在本章的例子中，我们利用shell的多行特性直接在命令行上运行了gawk脚本。在shell脚本中使用gawk时，应该将不同的gawk命令放到不同的行，这样会比较容易阅读和理解，不要在shell脚本中将所有的命令都塞到同一行。还有，如果你发现在不同的shell脚本中用到了同样的gawk脚本，记着将这段gawk脚本放到一个单独的文件中，并用-f参数来在shell脚本中引用它（参见第19章）。

## 自定义变量
跟其他典型的编程语言一样，gawk允许你定义自己的变量在程序代码中使用。gawk自定义变量名可以是任意数目的字母、数字和下划线，但不能以数字开头。重要的是，要记住gawk变量名区分大小写。
**1. 在脚本中给变量赋值**

在gawk程序中给变量赋值跟在shell脚本中赋值类似，都用赋值语句。
```bash
$ gawk '
> BEGIN{
> testing="This is a test"
> print testing
> }'
This is a test
$

```
print语句的输出是testing变量的当前值。跟shell脚本变量一样，gawk变量可以保存数值或文本值。
```bash
$ gawk '
> BEGIN{
> testing="This is a test"
> print testing
> testing=45
> print testing
> }'
This is a test
45
$

```
在这个例子中，testing变量的值从文本值变成了数值。

赋值语句还可以包含数学算式来处理数字值。
```bash
$ gawk 'BEGIN{x=4; x= x * 2 + 3; print x}'
11
$

```
如你在这个例子中看到的，gawk编程语言包含了用来处理数字值的标准算数操作符。其中包括求余符号（%）和幂运算符号（^或**）。
**2. 在命令行上给变量赋值**
也可以用gawk命令行来给程序中的变量赋值。这允许你在正常的代码之外赋值，即时改变变量的值。下面的例子使用命令行变量来显示文件中特定数据字段。
```bash
$ cat script1
BEGIN{FS=","}
{print $n}
$ gawk -f script1 n=2 data1
data12
data22
data32
$ gawk -f script1 n=3 data1
data13
data23
data33
$
```
这个特性可以让你在不改变脚本代码的情况下就能够改变脚本的行为。第一个例子显示了文件的第二个数据字段，第二个例子显示了第三个数据字段，只要在命令行上设置n变量的值就行。

使用命令行参数来定义变量值会有一个问题。在你设置了变量后，这个值在代码的BEGIN部分不可用。
```bash
$ cat script2
BEGIN{print "The starting value is",n; FS=","}
{print $n}
$ gawk -f script2 n=3 data1
The starting value is



$
```
可以用-v命令行参数来解决这个问题。它允许你在BEGIN代码之前设定变量。在命令行上，-v命令行参数必须放在脚本代码之前。
```bash
$ gawk -v n=3 -f script2 data1
The starting value is 3
data13
data23
data33
$
```
# 处理数组
为了在单个变量中存储多个值，许多编程语言都提供数组。gawk编程语言使用关联数组提供数组功能。

关联数组跟数字数组不同之处在于它的索引值可以是任意文本字符串。你不需要用连续的数字来标识数组中的数据元素。相反，关联数组用各种字符串来引用值。每个索引字符串都必须能够唯一地标识出赋给它的数据元素。如果你熟悉其他编程语言的话，就知道这跟散列表和字典是同一个概念。

后面几节将会带你逐步熟悉gawk程序中关联数组的用法。
## 定义数组变量
可以用标准赋值语句来定义数组变量。数组变量赋值的格式如下：
```bash
var[index] = element
```
其中var是变量名，index是关联数组的索引值，element是数据元素值。下面是一些gawk中数组变量的例子。
```bash
capital["Illinois"] = "Springfield"
capital["Indiana"] = "Indianapolis"
capital["Ohio"] = "Columbus"
```
在引用数组变量时，必须包含索引值来提取相应的数据元素值。
```bash
$ gawk 'BEGIN{
> capital["Illinois"] = "Springfield"
> print capital["Illinois"]
> }'
Springfield
$
```
## 遍历数组变量
关联数组变量的问题在于你可能无法知晓索引值是什么。跟使用连续数字作为索引值的数字数组不同，关联数组的索引可以是任何东西。

如果要在gawk中遍历一个关联数组，可以用for语句的一种特殊形式。
```bash
for (var in array)
{
  statements
}
```
这个for语句会在每次循环时将关联数组array的下一个索引值赋给变量var，然后执行一遍statements。重要的是记住这个变量中存储的是索引值而不是数组元素值。可以将这个变量用作数组的索引，轻松地取出数据元素值。
```bash
$ gawk 'BEGIN{
> var["a"] = 1
> var["g"] = 2
> var["m"] = 3
> var["u"] = 4
> for (test in var)
> {
>    print "Index:",test," - Value:",var[test]
> }
> }'
Index: u  - Value: 4
Index: m  - Value: 3
Index: a  - Value: 1
Index: g  - Value: 2
$

```
注意，索引值不会按任何特定顺序返回，但它们都能够指向对应的数据元素值。明白这点很重要，因为你不能指望着返回的值都是有固定的顺序，只能保证索引值和数据值是对应的。
## 删除数组变量
从关联数组中删除数组索引要用一个特殊的命令。
```bash
delete array[index]
```
删除命令会从数组中删除关联索引值和相关的数据元素值。
```bash
$ gawk 'BEGIN{
> var["a"] = 1
> var["g"] = 2
> for (test in var)
> {
>    print "Index:",test," - Value:",var[test]
> }
> delete var["g"]
> print "---"
> for (test in var)
>    print "Index:",test," - Value:",var[test]
> }'
Index: a  - Value: 1
Index: g  - Value: 2
---
Index: a  - Value: 1
$
```
一旦从关联数组中删除了索引值，你就没法再用它来提取元素值.

# 使用模式
gawk程序支持多种类型的匹配模式来过滤数据记录，这一点跟sed编辑器大同小异。第19章已经介绍了两种特殊的模式在实践中的应用。BEGIN和END关键字是用来在读取数据流之前或之后执行命令的特殊模式。类似地，你可以创建其他模式在数据流中出现匹配数据时执行一些命令。

本节将会演示如何在gawk脚本中用匹配模式来限定程序脚本作用在哪些记录上。
## 正则表达式
第20章介绍了如何将正则表达式用作匹配模式。可以用基础正则表达式（BRE）或扩展正则表达式（ERE）来选择程序脚本作用在数据流中的哪些行上。

在使用正则表达式时，正则表达式必须出现在它要控制的程序脚本的左花括号前。
```bash
$ gawk 'BEGIN{FS=","} /11/{print $1}' data1
data11
$
```
正则表达式/11/匹配了数据字段中含有字符串11的记录。gawk程序会用正则表达式对记录中所有的数据字段进行匹配，包括字段分隔符。
```bash
$ gawk 'BEGIN{FS=","} /,d/{print $1}' data1
data11
data21
data31
$
```
这个例子使用正则表达式匹配了用作字段分隔符的逗号。这也并不总是件好事。它可能会造成如下问题：当试图匹配某个数据字段中的特定数据时，这些数据又出现在其他数据字段中。如果需要用正则表达式匹配某个特定的数据实例，应该使用匹配操作符。
## 匹配操作符
匹配操作符（matching operator）允许将正则表达式限定在记录中的特定数据字段。匹配操作符是波浪线（~）。可以指定匹配操作符、数据字段变量以及要匹配的正则表达式。
```bash
$1 ~ /^data/
```
$1变量代表记录中的第一个数据字段。这个表达式会过滤出第一个字段以文本data开头的所有记录。下面是在gawk程序脚本中使用匹配操作符的例子。
```bash
$ gawk 'BEGIN{FS=","} $2 ~ /^data2/{print $0}' data1
data21,data22,data23,data24,data25
$
```
匹配操作符会用正则表达式/^data2/来比较第二个数据字段，该正则表达式指明字符串要以文本data2开头。

这可是件强大的工具，gawk程序脚本中经常用它在数据文件中搜索特定的数据元素。
```bash
$ gawk -F: '$1 ~ /rich/{print $1,$NF}' /etc/passwd
rich /bin/bash
$
```
这个例子会在第一个数据字段中查找文本rich。如果在记录中找到了这个模式，它会打印该记录的第一个和最后一个数据字段值。

你也可以用!符号来排除正则表达式的匹配。
```bash
$1 !~ /expression/
```
如果记录中没有找到匹配正则表达式的文本，程序脚本就会作用到记录数据。
```bash
$ gawk -F: '$1 !~ /rich/{print $1,$NF}' /etc/passwd
root /bin/bash
daemon /bin/sh
bin /bin/sh
sys /bin/sh
--- output truncated ---
$

```
在这个例子中，gawk程序脚本会打印/etc/passwd文件中与用户ID rich不匹配的用户ID和登录shell。
## 数学表达式
除了正则表达式，你也可以在匹配模式中用数学表达式。这个功能在匹配数据字段中的数字值时非常方便。举个例子，如果你想显示所有属于root用户组（组ID为0）的系统用户，可以用这个脚本。
```bash
$ gawk -F: '$4 == 0{print $1}' /etc/passwd
root
sync
shutdown
halt
operator
$
```

这段脚本会查看第四个数据字段含有值0的记录。在这个Linux系统中，有五个用户账户属于root用户组。

可以使用任何常见的数学比较表达式。
- x == y：值 x 等于y。

- x <= y：值 x 小于等于y。

- x < y：值 x 小于y。

- x >= y：值 x 大于等于y。

- x > y：值 x 大于y。

也可以对文本数据使用表达式，但必须小心。跟正则表达式不同，表达式必须完全匹配。数据必须跟模式严格匹配。
```bash
$ gawk -F, '$1 == "data"{print $1}' data1
$
$ gawk -F, '$1 == "data11"{print $1}' data1
data11
$
```
第一个测试没有匹配任何记录，因为第一个数据字段的值不在任何记录中。第二个测试用值data11匹配了一条记录。

# 结构化命令
gawk编程语言支持常见的结构化编程命令。本节将会介绍这些命令，并演示如何在gawk编程环境中使用它们。
## 	if语句
gawk编程语言支持标准的if-then-else格式的if语句。你必须为if语句定义一个求值的条件，并将其用圆括号括起来。如果条件求值为TRUE，紧跟在if语句后的语句会执行。如果条件求值为FALSE，这条语句就会被跳过。可以用这种格式：
```bash
if (condition)
   statement1
```
也可以将它放在一行上，像这样：
```bash
if (condition) statement1
```
下面这个简单的例子演示了这种格式的。
```bash
$ cat data4
10
5
13
50
34
$ gawk '{if ($1 > 20) print $1}' data4
50
34
$
```
并不复杂。如果需要在if语句中执行多条语句，就必须用花括号将它们括起来。
```bash
$ gawk '{
> if ($1 > 20)
> {
>   x = $1 * 2
>   print x
> }
> }' data4
100
68
$

```
注意，不能弄混if语句的花括号和用来表示程序脚本开始和结束的花括号。如果弄混了，gawk程序能够发现丢失了花括号，并产生一条错误消息。
```bash
$ gawk '{
> if ($1 > 20)
> {
>    x = $1 * 2
>    print x
> }' data4
gawk: cmd. line:6: }
gawk: cmd. line:6:  ^ unexpected newline or end of string
$

```
gawk的if语句也支持else子句，允许在if语句条件不成立的情况下执行一条或多条语句。这里有个使用else子句的例子。
```bash
$ gawk '{
> if ($1 > 20)
> {
>    x = $1 * 2
>    print x
> } else
> {
>    x = $1 / 2
>    print x
> }}' data4
5
2.5
6.5
100
68
$

```
可以在单行上使用else子句，但必须在if语句部分之后使用分号。
```bash
if (condition) statement1; else statement2
```
以下是上一个例子的单行格式版本。
```bash
$ gawk '{if ($1 > 20) print $1 * 2; else print $1 / 2}' data4
5
2.5
6.5
100
68
$
```
这个格式更紧凑，但也更难理解。
## while 语句
while语句为gawk程序提供了一个基本的循环功能。下面是while语句的格式。
```bash
while (condition)
{
  statements
}
```
while循环允许遍历一组数据，并检查迭代的结束条件。如果在计算中必须使用每条记录中的多个数据值，这个功能能帮得上忙。
```bash
$ cat data5
130 120 135
160 113 140
145 170 215
$ gawk '{
> total = 0
> i = 1
> while (i < 4)
> {
>    total += $i
>    i++
> }
> avg = total / 3
> print "Average:",avg
> }' data5
Average: 128.333
Average: 137.667
Average: 176.667
$
```
while语句会遍历记录中的数据字段，将每个值都加到total变量上，并将计数器变量i增值。当计数器值等于4时，while的条件变成了FALSE，循环结束，然后执行脚本中的下一条语句。这条语句会计算并打印出平均值。这个过程会在数据文件中的每条记录上不断重复。

gawk编程语言支持在while循环中使用break语句和continue语句，允许你从循环中跳出。
```bash
$ gawk '{
> total = 0
> i = 1
> while (i < 4)
> {
>    total += $i
>    if (i == 2)
>       break
>    i++
> }
> avg = total / 2
> print "The average of the first two data elements is:",avg
> }' data5
The average of the first two data elements is: 125
The average of the first two data elements is: 136.5
The average of the first two data elements is: 157.5
$
```

## do-while语句
do-while语句类似于while语句，但会在检查条件语句之前执行命令。下面是do-while语句的格式。
```bash
do
{
  statements
} while (condition)
```
这种格式保证了语句会在条件被求值之前至少执行一次。当需要在求值条件前执行语句时，这个特性非常方便。
```bash
$ gawk '{
> total = 0
> i = 1
> do
> {
>    total += $i
>    i++
> } while (total < 150)
> print total }' data5
250
160
315
$
```
这个脚本会读取每条记录的数据字段并将它们加在一起，直到累加结果达到150。如果第一个数据字段大于150（就像在第二条记录中看到的那样），则脚本会保证在条件被求值前至少读取第一个数据字段的内容。
## for语句
for语句是许多编程语言执行循环的常见方法。gawk编程语言支持C风格的for循环。
```bash
for( variable assignment; condition; iteration process)
```
将多个功能合并到一个语句有助于简化循环。
```bash
$ gawk '{
> total = 0
> for (i = 1; i < 4; i++)
> {
>    total += $i
> }
> avg = total / 3
> print "Average:",avg
> }' data5
Average: 128.333
Average: 137.667
Average: 176.667
$
```
定义了for循环中的迭代计数器，你就不用担心要像使用while语句一样自己负责给计数器增值了。
# 格式化打印
你可能已经注意到了print语句在gawk如何显示数据上并未提供多少控制。你能做的只是控制输出字段分隔符（OFS）。如果要创建详尽的报表，通常需要为数据选择特定的格式和位置。

解决办法是使用格式化打印命令，叫作printf。如果你熟悉C语言编程的话，gawk中的printf命令用法也是一样，允许指定具体如何显示数据的指令。

下面是printf命令的格式：
```bash
printf "format string", var1, var2 . . .
```
format string是格式化输出的关键。它会用文本元素和格式化指定符来具体指定如何呈现格式化输出。格式化指定符是一种特殊的代码，会指明显示什么类型的变量以及如何显示。gawk程序会将每个格式化指定符作为占位符，供命令中的变量使用。第一个格式化指定符对应列出的第一个变量，第二个对应第二个变量，依此类推。

格式化指定符采用如下格式：
```bash

%[modifier]control-letter
```

其中control-letter是一个单字符代码，用于指明显示什么类型的数据，而modifier则定义了可选的格式化特性。表22-3列出了可用在格式化指定符中的控制字母。
**表 22-3　格式化指定符的控制字母**

| 控制字母 |                  描述                   |
| ------- | --------------------------------------- |
| `c`     | 将一个数作为ASCII字符显示                |
| `d`     | 显示一个整数值                           |
| `i`     | 显示一个整数值（跟`d`一样）               |
| `e`     | 用科学计数法显示一个数                   |
| `f`     | 显示一个浮点值                           |
| `g`     | 用科学计数法或浮点数显示（选择较短的格式） |
| `o`     | 显示一个八进制值                         |
| `s`     | 显示一个文本字符串                       |
| `x`     | 显示一个十六进制值                       |
| `X`     | 显示一个十六进制值，但用大写字母A~F       |

因此，如果你需要显示一个字符串变量，可以用格式化指定符%s。如果你需要显示一个整数值，可以用%d或%i（%d是十进制数的C风格显示方式）。如果你要用科学计数法显示很大的值，就用%e格式化指定符。
```bash
$ gawk 'BEGIN{
> x = 10 * 100
> printf "The answer is: %e\n", x
> }'
The answer is: 1.000000e+03
$
```

除了控制字母外，还有3种修饰符可以用来进一步控制输出。

- width：指定了输出字段最小宽度的数字值。如果输出短于这个值，printf会将文本右对齐，并用空格进行填充。如果输出比指定的宽度还要长，则按照实际的长度输出。

- prec：这是一个数字值，指定了浮点数中小数点后面位数，或者文本字符串中显示的最大字符数。

- -（减号）：指明在向格式化空间中放入数据时采用左对齐而不是右对齐。

在使用printf语句时，你可以完全控制输出样式。举个例子，在22.1.1节，我们用print命令来显示数据行中的数据字段。
```bash
$ gawk 'BEGIN{FS="\n"; RS=""} {print $1,$4}' data2
Riley Mullen (312)555-1234
Frank Williams (317)555-9876
Haley Snell (313)555-4938
$
```
可以用printf命令来帮助格式化输出，使得输出信息看起来更美观。首先，让我们将print命令转换成printf命令，看看会怎样。
```bash
$ gawk 'BEGIN{FS="\n"; RS=""} {printf "%s %s\n", $1, $4}' data2
Riley Mullen  (312)555-1234
Frank Williams  (317)555-9876
Haley Snell  (313)555-4938
$

```
它会产生跟print命令相同的输出。printf命令用%s格式化指定符来作为这两个字符串值的占位符。

注意，你需要在printf命令的末尾手动添加换行符来生成新行。没添加的话，printf命令会继续在同一行打印后续输出。

如果需要用几个单独的printf命令在同一行上打印多个输出，这就会非常有用。
```bash
$ gawk 'BEGIN{FS=","} {printf "%s ", $1} END{printf "\n"}' data1
data11 data21 data31
$
```

每个printf的输出都会出现在同一行上。为了终止该行，END部分打印了一个换行符。

下一步，用修饰符来格式化第一个字符串值。
```bash
$ gawk 'BEGIN{FS="\n"; RS=""} {printf "%16s  %s\n", $1, $4}' data2
   Riley Mullen  (312)555-1234
 Frank Williams  (317)555-9876
    Haley Snell  (313)555-4938
$
```
通过添加一个值为16的修饰符，我们强制第一个字符串的输出宽度为16个字符。默认情况下，printf命令使用右对齐来将数据放到格式化空间中。要改成左对齐，只需给修饰符加一个减号即可。
```bash
$ gawk 'BEGIN{FS="\n"; RS=""} {printf "%-16s  %s\n", $1, $4}' data2
Riley Mullen      (312)555-1234
Frank Williams    (317)555-9876
Haley Snell       (313)555-4938
$
```
现在看起来专业多了！

printf命令在处理浮点值时也非常方便。通过为变量指定一个格式，你可以让输出看起来更统一。
```bash
$ gawk '{
> total = 0
> for (i = 1; i < 4; i++)
> {
>    total += $i
> }
> avg = total / 3
> printf "Average: %5.1f\n",avg
> }' data5
Average: 128.3
Average: 137.7
Average: 176.7
$

```

# 内建函数
gawk编程语言提供了不少内置函数，可进行一些常见的数学、字符串以及时间函数运算。你可以在gawk程序中利用这些函数来减少脚本中的编码工作。本节将 会带你逐步熟悉gawk中的各种内建函数。
## 数学函数
如果你有过其他语言的编程经验，可能就会很熟悉在代码中使用内建函数来进行一些常见的数学运算。gawk编程语言不会让那些寻求高级数学功能的程序员失望。

表22-4列出了gawk中内建的数学函数
|     函数      |                  描述                  |
| ------------- | -------------------------------------- |
| `atan2(x, y)` | *x*/*y* 的反正切，*x* 和*y* 以弧度为单位 |
| `cos(x)`      | *x* 的余弦，*x* 以弧度为单位             |
| `exp(x)`      | *x* 的指数函数                          |
| `int(x)`      | *x* 的整数部分，取靠近零一侧的值         |
| `log(x)`      | *x* 的自然对数                          |
| `rand( )`     | 比0大比1小的随机浮点值                   |
| `sin(x)`      | *x* 的正弦，*x* 以弧度为单位             |
| `sqrt(x)`     | *x* 的平方根                            |
| `srand(x)`    | 为计算随机数指定一个种子值               |


虽然数学函数的数量并不多，但gawk提供了标准数学运算中要用到的一些基本元素。int()函数会生成一个值的整数部分，但它并不会四舍五入取近似值。它的做法更像其他编程语言中的floor函数。它会生成该值和0之间最接近该值的整数。

这意味着int()函数在值为5.6时返回5，在值为-5.6时则返回-5。

rand()函数非常适合创建随机数，但你需要用点技巧才能得到有意义的值。rand()函数会返回一个随机数，但这个随机数只在0和1之间（不包括0或1）。要得到更大的数，就需要放大返回值。

产生较大整数随机数的常见方法是用rand()函数和int()函数创建一个算法。
```bash
x = int(10 * rand())
```
这会返回一个0～9（包括0和9）的随机整数值。只要为你的程序用上限值替换掉等式中的10就可以了。

在使用一些数学函数时要小心，因为gawk语言对于它能够处理的数值有一个限定区间。如果超出了这个区间，就会得到一条错误消息。
```bash
$ gawk 'BEGIN{x=exp(100); print x}'
26881171418161356094253400435962903554686976
$ gawk 'BEGIN{x=exp(1000); print x}'
gawk: warning: exp argument 1000 is out of range
inf
$
```

第一个例子会计算e的100次幂，虽然数值很大，但尚在系统的区间内。第二个例子尝试计算e的1000次幂，已经超出了系统的数值区间，所以就生成了一条错误消息。

除了标准数学函数外，gawk还支持一些按位操作数据的函数。

and(v1, v2)：执行值v1和v2的按位与运算。

compl(val)：执行val的补运算。

lshift(val, count)：将值val左移count位。

or(v1, v2)：执行值v1和v2的按位或运算。

rshift(val, count)：将值val右移count位。

xor(v1, v2)：执行值v1和v2的按位异或运算。

位操作函数在处理数据中的二进制值时非常有用。

## 字符串函数
gawk编程语言还提供了一些可用来处理字符串值的函数，如表22-5所示。

**表 22-5　gawk字符串函数**

|               函数               |                                                                                            描述                                                                                            |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `asort(*s* [,*d*])`              | 将数组*`s`*按数据元素值排序。索引值会被替换成表示新的排序顺序的连续数字。另外，如果指定了*`d`*，则排序后的数组会存储在数组*`d`*中                                                                  |
| `asorti(*s* [,*d*])`             | 将数组*`s`*按索引值排序。生成的数组会将索引值作为数据元素值，用连续数字索引来表明排序顺序。另外如果指定了*`d`*，排序后的数组会存储在数组*`d`*中                                                     |
| `gensub(*r*, *s*, *h* [, *t*])`  | 查找变量`$0`或目标字符串*`t`*（如果提供了的话）来匹配正则表达式`r`。如果*`h`*是一个以*`g`*或*`G`*开头的字符串，就用*`s`*替换掉匹配的文本。如果*`h`*是一个数字，它表示要替换掉第*`h`*处*`r`*匹配的地方 |
| `gsub(*r*, *s* [,*t*])`          | 查找变量`$0`或目标字符串*`t`*（如果提供了的话）来匹配正则表达式*`r`*。如果找到了，就全部替换成字符串*`s`*                                                                                        |
| `index(*s*, *t*)`                | 返回字符串*`t`*在字符串*`s`*中的索引值，如果没找到的话返回`0`                                                                                                                                 |
| `length([*s*])`                  | 返回字符串*`s`*的长度；如果没有指定的话，返回`$0`的长度                                                                                                                                       |
| `match(*s*, *r* [,*a*])`         | 返回字符串*`s`*中正则表达式*`r`*出现位置的索引。如果指定了数组*`a`*，它会存储*`s`*中匹配正则表达式的那部分                                                                                       |
| `split(*s*, *a* [,*r*])`         | 将*`s`*用`FS`字符或正则表达式*`r`*（如果指定了的话）分开放到数组*`a`*中。返回字段的总数                                                                                                         |
| `sprintf(*format*, *variables*)` | 用提供的*`format`*和*`variables`*返回一个类似于`printf`输出的字符串                                                                                                                          |
| `sub(*r*, *s* [,*t*])`           | 在变量`$0`或目标字符串*`t`*中查找正则表达式`r`的匹配。如果找到了，就用字符串*`s`*替换掉第一处匹配                                                                                                |
| `substr(*s*, *i* [,*n*])`        | 返回*`s`*中从索引值*`i`*开始的*`n`*个字符组成的子字符串。如果未提供*`n`*，则返回*`s`*剩下的部分                                                                                                 |
| `tolower(*s*)`                   | 将*`s`*中的所有字符转换成小写                                                                                                                                                               |
| `toupper(*s*)`                   | 将*`s`*中的所有字符转换成大写                                                                                                                                                               |


一些字符串函数的作用相对来说显而易见
```bash
$ gawk 'BEGIN{x = "testing"; print toupper(x); print length(x) }'
TESTING
7
$
```

但一些字符串函数的用法相当复杂。asort和asorti函数是新加入的gawk函数，允许你基于数据元素值（asort）或索引值（asorti）对数组变量进行排序。这里有个使用asort的例子

```bash
$ gawk 'BEGIN{
> var["a"] = 1
> var["g"] = 2
> var["m"] = 3
> var["u"] = 4
> asort(var, test)
> for (i in test)
>     print "Index:",i," - value:",test[i]
> }'
Index: 4  - value: 4
Index: 1  - value: 1
Index: 2  - value: 2
Index: 3  - value: 3
$

```
新数组test含有排序后的原数组的数据元素，但索引值现在变为表明正确顺序的数字值了。

split函数是将数据字段放到数组中以供进一步处理的好办法.
```bash
$ gawk 'BEGIN{ FS=","}{
> split($0, var)
> print var[1], var[5]
> }' data1
data11 data15
data21 data25
data31 data35
$
```

新数组使用连续数字作为数组索引，从含有第一个数据字段的索引值1开始。

## 时间函数
**表 22-6　gawk的时间函数**
|                函数                 |                                          描述                                           |
| ----------------------------------- | --------------------------------------------------------------------------------------- |
| `mktime(*datespec*)`                | 将一个按YYYY MM DD HH MM SS \[DST\]格式指定的日期转换成时间戳值**1**                      |
| `strftime(*format* [,*timestamp*])` | 将当前时间的时间戳或timestamp（如果提供了的话）转化格式化日期（采用shell函数`date()`的格式） |
| `systime( )`                        | 返回当前时间的时间戳                                                                     |

> 1这里时间戳是指自1970-01-01 00:00:00 UTC到现在，以秒为单位的计数，通常称为epoch time。systime()函数的返回值也是这种形式。

时间函数常用来处理日志文件，而日志文件则常含有需要进行比较的日期。通过将日期的文本表示形式转换成epoch时间（自1970-01-01 00:00:00 UTC到现在的秒数），可以轻松地比较日期。

下面是在gawk程序中使用时间函数的例子。
```bash
$ gawk 'BEGIN{
> date = systime()
> day = strftime("%A, %B %d, %Y", date)
> print day
> }'
Friday, December 26, 2014
$
```