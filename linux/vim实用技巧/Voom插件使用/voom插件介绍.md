---
title: voom插件介绍
description: voom插件介绍
published: true
date: 2021-08-23T03:32:46.872Z
tags: 介绍, voom
editor: markdown
dateCreated: 2020-07-01T16:02:38.004Z
---

# Voom 插件介绍
Voom(基于vim 的外部标记文档)的插件,模拟了两个窗口的(大纲和文本)

有关Voom 概述的快速介绍,请见[VOom快速开始](/zh/linux/vim实用技巧/Voom插件使用/Voom快速开始).
有关所有Voom 命令的简明列表(参考手册)),请参见Voom-map.

Voom 最初是用来处理带有编号折叠的标记文档.

Voom 支持20种以上的标记格式文档,有些格式有标题且支持大纲结构,包裹轻量级标记语言,如`reST`,`Markdown`, `Pandoc`, `AsciiDoc`, `Org-mode`, `Wiki`, `LaTeX`, `etc`(标题也成为标题,节标题)


有四个主要的Ex 命令: `Voom`, `Voomhelp`, `Voomexec`, `Voomlog`.


`:Voom` `{MarkupMode}`
	创建当前缓冲区的大纲.扫描当前缓冲区的标题.将标题显示到大纲窗口中.标题的格式{MarkupMode} (例子: :`Voom makrdown`,然后就会分析大纲,并展示).
  大纲显示再一个单独的特殊缓冲区中模拟了一个双窗口outliner 的树状窗格.这样的缓冲区为树缓冲区.可以在导航树中操作:向上/向下,提高优先级/降低优先级,复制/剪切/粘贴,标记/不标记,排序,随机化,等等.
  
`:Voom` 
 使用默认标记模式,默认为"fmr" 模式: 这种标记文档是带有起始折叠标记(由VIM 指定) ,例子{{{1,{{{2,{{{3等.标题的层级是数字,标题的文本是`{{`之前的部分.这种标记文本格式请参见[fmr](/zh/linux/vim实用技巧/Voom插件使用/标记类型)



`:Voomhelp`
在新标签页打开`voom.txt` ,`voom.txt`是一个帮助文档.

Voom 还包含两个`vim` 和`python`的相关实用命令.
`:Voomexec` 	i可以通过这个命令执行当前内容以`vim scripts` 或`python scripts`,这个功能对于调试代码片段很有用.

`:Voomlog` 
创建缓冲区把`python` 的输出`sys.stdout`和`stderr`重定向,这在开发python 脚本时非常有用.
