---
title: md5sum
description: md5sum 命令使用
published: true
date: 2021-12-07T03:12:39.172Z
tags: md5sum
editor: markdown
dateCreated: 2021-12-07T03:12:39.172Z
---

# md5sum
相关命令：暂无相关命令
用法：md5sum [选项]... [文件]...
显示或检查 MD5(128-bit) 校验和。
若没有文件选项，或者文件处为"-"，则从标准输入读取。

  -b, --binary          以二进制模式读取
  -c, --check           从文件中读取MD5 的校验值并予以检查
  -t, --text            以纯文本模式读取(默认)

以下三个选项在进行校验时非常有用：
      --quiet           不为校验成功的文件输出OK
      --status          不输出任何内容，使用退出状态号显示成功
  -w, --warn            对格式不准确的校验和行进行警告

      --strict         with --check, exit non-zero for any invalid input
      --help            显示此帮助信息并退出
      --version         显示版本信息并退出

校验和会按照RFC 1321 规范生成。当进行检查时，给出的输入格式应该和程序的输出
样板格式相同。默认的输出模式时输出一行校验和的校验结果，并有一个字符来
表示文件类型("*"代表二进制，" "代表纯文本)，并同时显示每个文件的名称。
# 常用例子
1. 以二进制进行读取
```bash
[root@linux ~]# md5sum -b test            
832df9d3d18471d80d67bee644eebb8a *test
```
2.  生成md5加密检验和    

```bash
[root@zhangwei scripts]# md5sum zzz > zzz.md5
```
3. 检验与文件是否一致,
```bash
[root@zhangwei scripts]# md5sum -c zzz.md5
```
