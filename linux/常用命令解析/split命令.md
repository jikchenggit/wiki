---
title: split命令
description: split命令
published: true
date: 2021-08-23T03:37:31.784Z
tags: split
editor: markdown
dateCreated: 2020-07-19T06:05:17.827Z
---

split 命令：将指定的文件切割小的文件

-d 使用数字而不是字母作为切割后的小文件的后缀；
-v 显示详细的处理信息
-b<字节> 每个分割文件的大小
-C <数字> 指定输出到每一个文件的每一行的大小，数字后缀可以是
b: 512(blocks)
K: 1024(kibiBytes)
KB: 1000(kiloBytes)
M: 1024*1024(mebiBytes)
MB: 1000*1000(megaBytes)
G: 1024*1024*1024(gibiBytes)
GB: 1000*1000*1000(gibaBytes)
T, P, E, Z, Y
-l<行数> 指定切割的行数作为切割文件的单位；
--help 显示帮助信息
--version 显示版本信息