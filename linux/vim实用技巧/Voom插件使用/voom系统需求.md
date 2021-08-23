---
title: voom系统需求
description: voom系统需求
published: true
date: 2021-08-23T03:32:54.072Z
tags: voom系统需求
editor: markdown
dateCreated: 2020-07-02T02:02:54.759Z
---

# 需求
voom 需要vim 或gvim,且版本大于7.2 ,编译`正常`或更大的特性集和`python`接口.支持`python2` 和`python3`.

- 检查python2
```vim
:py print 2**0.5 
:py import sys; print sys.version
```
- 检查python3
```vim
:py3 print(2**0.5)
:py3 import sys; print(sys.version)
```
默认情况下,Voom 将使用第一个python 版本;首先尝试`python2`,然后尝试`python3`.要始终使用`python2`,然后尝试`python3`.
如果要始终使用`python2` ,添加以下语句到`.vimrc`文件中.
```vim
 let g:voom_python_versions = [2]	
```
使用`python3` ,添加以下语句到`.vimrc` 文件中
```vim
let g:voom_python_versions = [3]
```
先尝试`python3` ,后尝试`python2`
```vim
let g:voom_python_versions = [3,2]   
```
查看当前使用的`python` 版本.
```vim
Voominfo [all]
```

                                              
                                              