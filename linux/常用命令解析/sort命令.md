---
title: sort命令
description: sort 命令介绍
published: true
date: 2021-08-23T03:26:58.769Z
tags: 命令, sort
editor: markdown
dateCreated: 2020-03-20T12:03:12.384Z
---

# sort 命令
处理大量数据时的一个常用命令是sort命令。顾名思义，sort命令是对数据进行排序的。

默认情况下，sort命令按照会话指定的默认语言的排序规则对文本文件中的数据行排序。
## sort 命令参数说明:

| 单破折线 |            双破折线            |                                    描述                                     |
| ------- | ----------------------------- | -------------------------------------------------------------------------- |
| `-b`    | `--ignore-leading-blanks`     | 排序时忽略起始的空白                                                         |
| `-C`    | `--check=quiet`               | 不排序，如果数据无序也不要报告                                                |
| `-c`    | `--check`                     | 不排序，但检查输入数据是不是已排序；未排序的话，报告                           |
| `-d`    | `--dictionary-order`          | 仅考虑空白和字母，不考虑特殊字符                                              |
| `-f`    | `--ignore-case`               | 默认情况下，会将大写字母排在前面；这个参数会忽略大小写                          |
| `-g`    | `--general-number-sort`       | 按通用数值来排序（跟`-n`不同，把值当浮点数来排序，支持科学计数法表示的值）       |
| `-i`    | `--ignore-nonprinting`        | 在排序时忽略不可打印字符                                                     |
| `-k`    | `--key=*POS1*[,*POS2*]`       | 排序从POS1位置开始；如果指定了POS2的话，到POS2位置结束                         |
| `-M`    | `--month-sort`                | 用三字符月份名按月份排序                                                     |
| `-m`    | `--merge`                     | 将两个已排序数据文件合并                                                     |
| `-n`    | `--numeric-sort`              | 按字符串数值来排序（并不转换为浮点数）                                        |
| `-o`    | `--output=*file*`             | 将排序结果写出到指定的文件中                                                  |
| `-R`    | `--random-sort`               | 按随机生成的散列表的键值排序                                                  |
|         | `--random-source=*FILE*`      | 指定`-R`参数用到的随机字节的源文件                                            |
| `-r`    | `--reverse`                   | 反序排序（升序变成降序）                                                     |
| `-S`    | `--buffer-size=*SIZE*`        | 指定使用的内存大小                                                           |
| `-s`    | `--stable`                    | 禁用最后重排序比较                                                           |
| `-T`    | `--temporary-directory=*DIR*` | 指定一个位置来存储临时工作文件                                                |
| `-t`    | `--field-separator=*SEP*`     | 指定一个用来区分键位置的字符                                                  |
| `-u`    | `--unique`                    | 和`-c`参数一起使用时，检查严格排序；不和`-c`参数一起用时，仅输出第一例相似的两行 |
| `-z`    | `--zero-terminated`           | 用NULL字符作为行尾，而不是用换行符   |
# 使用场景案例
## 按照文件大小排序


```bash
$ du -sh * | sort -nr
1008k   mrtg-2.9.29.tar.gz
972k    bldg1
888k    fbs2.pdf
760k    Printtest
680k    rsync-2.6.6.tar.gz
660k    code
516k    fig1001.tiff
496k    test
496k    php-common-4.0.4pl1-6mdk.i586.rpm
448k    MesaGLUT-6.5.1.tar.gz
400k    plp
```

## 去除重复行
它的作用很简单，就是在输出行中去除重复行。

```sh
[rocrocket@rocrocket programming]$ cat seq.txt
banana
apple
pear
orange
pear
[rocrocket@rocrocket programming]$ sort seq.txt

apple
banana
orange
pear
pear
[rocrocket@rocrocket programming]$ sort -u seq.txt
apple
banana
orange
pear
```


> pear由于重复被-u选项无情的删除了。
{.is-warning}



## 指定关键字排序
-k和-t参数在对按字段分隔的数据进行排序时非常有用，例如/etc/passwd文件。可以用-t参数来指定字段分隔符，然后用-k参数来指定排序的字段。举个例子，要对前面提到的密码文件/etc/passwd根据用户ID进行数值排序，可以这么做.

```bash
$ sort -t ':' -k 3 -n /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
news:x:9:13:news:/etc/news:
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
gopher:x:13:30:gopher:/var/gopher:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
```
## 按照每列关键字去重

```console
sort  -t ' ' -k 1,3 -u 
```


