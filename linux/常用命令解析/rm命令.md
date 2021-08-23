---
title: rm命令
description: rm命令
published: true
date: 2021-08-23T03:37:49.780Z
tags: rm
editor: markdown
dateCreated: 2020-07-19T06:10:49.060Z
---

用法：rm [选项]... 文件...
删除 (unlink) 文件。

  -f, --force           强制删除。忽略不存在的文件，不提示确认
  -i                    在删除前需要确认
  -I                    在删除超过三个文件或者递归删除前要求确认。此选项比-i 提
                        示内容更少，但同样可以阻止大多数错误发生
      --interactive[=WHEN]      根据指定的WHEN 进行确认提示：never，once (-I)，
                                或者always (-i)。如果此参数不加WHEN 则总是提示
      --one-file-system         递归删除一个层级时，跳过所有不符合命令行参
                                数的文件系统上的文件
      --no-preserve-roo 不特殊对待"/"
      --preserve-root   不允许删除"/"(默认)
  -d, --dir	删除空目录
  -r, -R, --recursive   递归删除目录及其内容
  -v, --verbose         详细显示进行的步骤
      --help            显示此帮助信息并退出
      --version         显示版本信息并退出

默认时，rm 不会删除目录。使用--recursive(-r 或-R)选项可删除每个给定
的目录，以及其下所有的内容。

要删除第一个字符为"-"的文件 (例如"-foo")，请使用以下方法之一：
  rm -- -foo
  rm ./-foo

请注意，如果使用rm 来删除文件，通常仍可以将该文件恢复原状。如果想保证
该文件的内容无法还原，请考虑使用shred。