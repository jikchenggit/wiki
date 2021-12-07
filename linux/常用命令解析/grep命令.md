---
title: grep命令
description: grep命令介绍
published: true
date: 2021-12-07T02:47:17.286Z
tags: 命令, grep
editor: markdown
dateCreated: 2020-03-20T12:05:33.129Z
---

# 1. Grep 命令
## 1.1. 匹配器选择:

```
-E, 扩展正则表达式
    将 模式解释为扩展正则表达式(-E 使用的时POSIX )
-F , 固定字符串,固定表达式
    将模式解释成为固定的字符串列表,指定的字符串都要匹配.
-G ,基本的正则表达式
    将模式解释成一个基本的正则表达式.
-P ,perl-方式的正则表达式
```
## 1.2. 匹配控制:
```bash
-e PATTERN, --regexp=PATTERN
    使用模式作为表达式.可以用来指定多个搜索模式.(POSIX 规范).
-f FILE, --file=FILE
    从文件中获取 表达式,每行一个.空文件包含零模式,因此不匹配任何内容.(POSIX 规范)
-i, --ignore-case
    忽略模式和输入文件中的大小写差别(POSIX 规范)
-v, --invert-match
    选择不匹配的项 .(POSIX 规范)
-w, --word-regexp
    只选择包含整个单词的匹配行.单词只能是字母,数字和下划线.
-x, --line-regexp
    筛选完全匹配的匹配项(面向行)
    [vim@dbserver grep]$ grep  -x 'The penny farthing is 10 paces ahead of Waldo.' *.txt 
goldrush.txt:The penny farthing is 10 paces ahead of Waldo.

-y     Obsolete synonym for -i.
    和i 一样的功能
```
## 1.3. 输出控制:

```

-c, --count
    抑制正常输出,打印每个文件的匹配行数,使用-v 将计算不匹配的行数.(POSIX)


--color[=WHEN], --colour[=WHEN]
    使用不同的颜色显示匹配项.

-L, --files-without-match
    抑制正常输出,打印每个输入文件的名称,但是只是到第一个匹配之后,扫描就会停止

-l, --files-with-matches
    抑制正常输出,打印每个输入文件的名称,但是只是到第一个匹配之后,扫描就会停止

-m NUM, --max-count=NUM
    匹配个数最大限定,如果达到了最大值则停止读取文件.如果输入是一个常规文件,且
    那么grep将确保标准输入定位到退出前最后匹配行之后的位置，而不管是否存在尾随上下文行。这使调用进程能够恢复搜索。当grep在NUM匹配行之后停止时，它输出任何尾随的上下文行 .
    使用-c 或者count 选项时,grep 输出计数不会大于NUM,使用-v 选项时`,grep 在输出第一个不匹配的项目之后停止.

-o, --only-matching
    只打印匹配行中匹配的部分(非空,只把匹配字段输出,并不是把行输出),将匹配的部分放再单独的输出行中.

-q, --quiet, --silent
    静默方式,不向标准输出写入任何内容,如果找到匹配先,立即退出,退出码为0,检测到错误也是如此.(POSIX)

-s, --no-messages
    抑制文件不存在,或者不可读的错误消息.一致性注意:第7版的unix grep 不符合POSIX ,因为缺少-q,USG风格grep 也缺少-q,但-s 选项表现的想GNU grep .
```

## 1.4. 输出行前缀控制

```
-b, --byte-offset
    打印匹配当前行的前几行.

-H, --with-filename
    打印每个匹配项的文件名,搜索多个文件的默认值.

-h, --no-filename
    禁止输出文件名,当只有一个文件或者标准输入时搜索的时候,是默认值.

--label=LABEL
    将来自标准输入的显示为来自标签输入.--label 将会给标准输入一个文件名.
    eg1 :gzip  -cd foo.gz | grep --label=foo -H something.  See also the -H option.
    eg2:
        [vim@dbserver grep]$ cat *.txt | grep -H --label=txt Waldo 
        txt:Waldo is beside the boot counter.
        txt:Waldo is studying his clipboard.
        txt:The penny farthing is 10 paces ahead of Waldo.

-n, --line-number
    在匹配的每一行 ,打印上下文 为几行.并显示行号.

-T, --initial-tab
    使用制表符规范输出结果.
    eg:
    [vim@dbserver grep]$ grep -T  Waldo *.txt   
    department-store.txt   :Waldo is beside the boot counter.
    goldrush.txt   :Waldo is studying his clipboard.
    goldrush.txt   :The penny farthing is 10 paces ahead of Waldo.

-u, --unix-byte-offsets
    unix 风格的字节偏移量.

-Z, --null
    输出一个零字节(ASCII NULL 字符),这个选项使输出没有起义.即使存在包含不寻常的字符(如换行)的文件名.
```


## 1.5. 文本控制
```
-A NUM, --after-context=NUM
    打印匹配行之前的多少行.(-o 没有效果,并会发出警告)

-B NUM, --before-context=NUM(-o 没有效果,并会发出警告)
    打印匹配行之后的多少行

-C NUM, -NUM, --context=NUM(-o 没有效果,并会发出警告)
    打印出输出上下问的行数.

--group-separator=SEP
    使用SEP作为分割符,默认的分割符为(--). 
--no-group-separator
    使用空字符串作为分割符.
```
## 1.6. 选则文件目录
```
-a, --text
    处理文本一样处理二进制文件.

--binary-files=TYPE
    如果文件的前几个字节表示该文件包含二进制数据,则假定该文件类型为type ,默认情况下type 是二进制的,grep 通常输出一行消息,说明二进制匹配,如果没有匹配,则不输出消息.如果类型不匹配,

-d ACTION, --directories=ACTION
    如果输入文件是一个目录,则-d 选项可以处理对于目录的动作.默认情况下ACTION是`read`目录,如果ACTION是`skip`则跳过目录.如果递归的,则ACTION 是 `recurse`.这个选项等价于`-r` 选项

--exclude=GLOB
    排除于GLOB 匹配的文件(是哟个通配符),文件名GLOB 可以使用 * , ? and [...] 作为通配符.

--exclude-from=FILE
    排除从文件中读取的全局匹配的文件(可以使用通配符)

--exclude-dir=DIR
    递归搜索中排除与模式DIR 匹配的目录.

-I     
    处理二进制文件.

--include=GLOB
    包含所有GLOB 匹配的文件(使用通配符),文件名GLOB 可以使用*, ? and [...] 为通配符.

-r, --recursive
    递归按照符号链接读取每个目录下的所有文件.

-R, --dereference-recursive
    递归的读取每个目录下的所有链接,包括符号链接.
```
## 1.7. 正则表达式
### 1.7.1. 字符类和括号表达式
括号表达式  使用[ 和] 字符列表.意义是匹配列表中的任何一个字符;
如果列表第一个是^ 符号,那么他是不匹配列表中的任何一个字符;
例如: [0123456789] 匹配任何的单个数字.

括号表达式中,范围表达式为连接字符组成,匹配两个字符之间的排序的任何单个字符.
例如:

    [a-d]: 等价语[abcd]

括号表达式预定义了一些指定的字符类:
   例如:

    [:alnum:]:字母和数字,

|     表达式      |                                                                         示意                                                                         |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **\[:alpha:\]** | 匹配当前归类中的大写和小写字母字符。例如，`'[0-9]{3}[[:alpha:]]{2}'` 匹配三个数字，后跟两个字母                                                           |
| **\[:alnum:\]** | 匹配当前归类中的数字、大写和小写字母字符。例如，`'[[:alnum:]]+'` 匹配含有一个或多个字母和数字的字符串。                                                    |
| **\[:cntrl:\]** | 匹配顺序值小于 32 或字符值为 127 的 ASCII 字符（控制字符）。控制字符包括换行符、换页符、退格符，等等                                                       |
| **\[:digit:\]** | 匹配当前归类中的数字。例如，`'[[:digit:]-]+'` 匹配含有一个或多个数字或横线的字符串。同样，`'[^[:digit:]-]+'` 匹配含有一个或多个不是数字或横线的字符的字符串。 |
| **\[:graph:\]** | 匹配打印字符。`[[:graph:]]` 等效于 `[[:alnum:][:punct:]]`。                                                                                           |
| **\[:lower:\]** | 匹配当前归类中的小写字母字符。例如，`'[[:lower:]]'` 不匹配 A，因为 A 为大写。                                                                            |
| **\[:print:\]** | 匹配打印字符和空格。`[[:print:]]` 等效于 `[[:graph:][:whitespace:]]`。                                                                                 |
| **\[:punct:\]** | 匹配其中一个字符： !"#$%&'()*+,-./:;<=>?@\[\\\]^_`{|}~.`[:punct:]` 子字符类不能包括当前归类中可用的非 ASCII 标点字符。                                    |
| **\[:space:\]** | 匹配单个空格 (' ')。例如，以下语句搜索 Contacts.City 以查找任何名称为两个词的城市：                                                                       |
| **\[:upper:\]** | 匹配当前归类中的大写字母字符。例如，`'[[:upper:]ab]'` 与以下其中一项匹配：任何大写字母、a 或 b。 |
| **\[:xdigit:\]** | 匹配字符类 \[0-9A-Fa-f\] 中的字符。 |

### 1.7.2. 定位符号
插入符号^ 和$ 都是元字符,匹配行首和空字符串.
### 1.7.3. 反斜杠的特殊表达式

\< 匹配单词开头和结尾空字符串.\b 匹配单词边缘的空字符串.\w 是  [_[:alnum:]]  的同义词,\W 是[^_[:alnum:]] 同义词.
### 1.7.4. 重复匹配
| 符号  |         匹配意义          |
| ----- | ------------------------ |
| ?     | 匹配0 次和 1 次           |
| *     | 匹配0次和多次.            |
| +     | 匹配一次或多次.           |
| {n}   | 刚好匹配 n 次.            |
| {n,}  | 匹配n 次或更多次.         |
| {,m}  | 最多匹配m次.              |
| {n,m} | 最少匹配n次,但最多匹配m次. |

### 1.7.5. 优先级
重复优先于连接,连接优先于 | (或 ),使用() 来改变优先级.
### 1.7.6. 匹配引用
使用 \n 用来 引用前面的第n 个() 表达式匹配的字符串.
### 1.7.7. 基本正则表达式和 扩展正则表达式
在 基本正则表达式中 :元字符: `?`,` +` ,` { `,` |` , ( 和) 失去了特殊含义,如果想使用,则需要前面加上反斜杠.  `\?`, `\+`, `\{`, `\|`, `\(`, and` \)`.
传统的egrep  不支持 `{` 元字符,而 egrep 支持` \{` ,可移植的脚本应该避免使用 `{` ,应该使用` [{]` 匹配文字.
`GNU grep -E`试图支持传统用法，如果{是一个无效区间规范的开始，。
例如，命令
`grep -E '{1'`搜索双字符串{1，而不是报告正则表达式中的语法错误。POSIX允许这种行为作为扩展，但是可移植脚本应该避免这种情况.

```shell
grep -C 5 foo file 显示file文件里匹配foo字串那行以及上下5行
grep -B 5 foo file 显示foo及前5行
grep -A 5 foo file 显示foo及后5行
```

查看grep版本号的方法是
```shell
grep -V
```

grep 指定行输出
```console
grep -vE 'Database|^$|colname' 
```

```console

grep -vE 'Database|^$|colname|SELECT|WHERE|AND|FROM'
```
# 常用例子
1. 完整输出去掉空行及注释
```console
grep -Ev “^$|#” 
```