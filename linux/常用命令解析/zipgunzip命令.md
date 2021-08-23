---
title: zip和gunzip命令
description: zip和gunzip命令
published: true
date: 2021-08-23T03:27:14.989Z
tags: gzip, gunzip, gzcat
editor: markdown
dateCreated: 2020-03-20T13:05:04.770Z
---

# 1. gzip和 gunzip 命令

|           选项           |                                                                  描述                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| -d, --decompress         | 解压软件                                                                                                                               |
| -f, --force              | 如果文件已经存在,使用选项强制压缩覆盖当前文件.                                                                                            |
| -c, --stdout --to-stdout | 将输出写入标准输出;保持原始文件不变,可以通过管道进行一次性打包.                                                                            |
| -l, --list               | 对于每个压缩文件,列出以下字段:<br/> 压缩大小:压缩文件的大小<br/>未压缩大小:未压缩文件的大小 <br/> 比值:压缩比(未知0.0%)<br/>:未压缩文件的名称. |
| -n, --no-name            | 不保存原始的名称和时间戳,解压出来的名称,就是把gz 去掉的名称.                                                                               |
| -N, --name               | 保存原始的名称和时间戳,对于传输后文件名被截断,非常有用.                                                                                    |
| -r, --recursive          | 对所有子目录进行递归压缩(解压)操作,                                                                                                      |
| -t, --test               | 检查压缩文件的完整性                                                                                                                    |
| -v, --verbose            | 详细显示每个文件的名称和压缩比                                                                                                           |
| -1, --fast               | compress faster                                                                                                                        |
| -9, --best               | compress better                                                                                                                        |
# 2. 高级使用
## 2.1. 对一个压缩包进行文件添加
```bash
gzip -c file1  > foo.gz
gzip -c file2 >> foo.gz
```

## 2.2. 进行查看内容

```bash
gunzip -c foo
```

## 2.3. 一次性打包多个文件
```bash
gzip -c file1 file2 > foo.gz
```

