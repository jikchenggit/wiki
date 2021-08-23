---
title: voom快速使用手册
description: voom快速使用手册
published: true
date: 2021-08-23T03:33:34.351Z
tags: voom快速使用手册
editor: markdown
dateCreated: 2020-07-06T09:22:11.619Z
---

# 快速入门
演示了最基本使用voom 的命令和原则. 
有关所有voom 命令,请参见[voom-map](/zh/linux/vim实用技巧/Voom插件使用/voom-map)章节

## 创建大纲
创建一个新的标签页(`:tabnew`),并粘贴以下行
```
    Headline A {{{1
        some text
            
        Headline B {{{2=
        more text
            
        Headline C {{{3

        Headline D {{{1o
            
        Headline E {{{2x
```
键扫描当前缓冲区的标题,创建大纲,并显示树缓冲区,显示为如下截图:
![voom_tree_cache.png](/voom/voom_tree_cache.png)

- 切换到Body buffer(CTRAL+ W w) 缓冲区, 修改标题后,切换到大纲会自动刷新. 

- 光标在大纲视图内时,按q 键会删除大纲和树缓冲区.当前树缓冲区被删除时,大纲也会被自动删除.


当然可以使用`voom` 指定创建不同的大纲模式
```vim
:Voom org

:Voom markdown
```
如果你创建大纲视图创建错了. 请按`q` 键,然后重新创建大纲视图.
- asciidoc文档格式
[asciidoc文档格式](http://asciidoc.org/userguide.txt)
```
:Voom asciidoc  
```
- reStructuredText文档格式

[reStructuredText文档格式](http://docutils.sourceforge.net/docs/ref/rst/restructuredtext.txt)

 ```
 :Voom rest 
 ```
- pandoc 文档格式                            
[pandoc文档格式](https://github.com/jgm/pandoc/blob/master/MANUAL.txt)

```bash
:Voom pandoc 
```
- LaTex文档格式             
[LaTex文档格式](https://github.com/AllenDowney/ThinkPython2/blob/master/book/book.tex)
```
:Voom latex   
```
## 在树缓冲区和文件缓冲区切换
由于此插件处理的时两个窗口, 因此在两个窗口之间切换非常重要.默认情况下,使用`<Enter> ` 和`<TAB>`  进行快速切换.
技巧点:
 1. 选择对应的标题, 使用`<TAB>` 键或`<Enter>`键跳转到文件缓冲区的内容位置.
 2. 使用`<空格键>`打开或者折叠当前标题下的子标题.或者使用`vim` 标准命令(zo,zc,zR,zM) 也可以展开/收缩节点.
 3. 使用`P` 可以移动到当前标题的父标题.
 
 更多命令请查看[voom-map](/zh/linux/vim实用技巧/Voom插件使用/voom-map)
## 编辑标题
选择想要编辑的标题,然后光标移动到`Body` 窗口, 使用标准`vim` 修改.
技巧点:
	 1. 当在大纲视图里,使用大写的`I`,将会在本标题节点最后增加文本.
## 增加新标题
`aa` 增加新标题,与当前标题平级.
`AA` 增加新标题,是当前标题的子标题. 
## 操作大纲视图
若想重新排列大纲结构,光标必须位于大纲视图中.
(在`normal` 状态下) 可以对标题进行操作,在可视模式下,可以批量操作大纲视图里的标题.
技巧点:

1. <C-Up>, <C-Down> 上下移动节点,调节章节顺序.
2. <C-Left>, <C-Right> 升级或者降级.
说明: 如果`<CTRL>` 不可用,请使用以下命令,也可以达到相同的效果.
  `  ^^  __  <<  >> `
3. 可以对大纲视图进行复制粘贴,删除操作.
  `yy` : 复制节点所有内容到`+寄存器` (系统剪切板)
  `dd` : 删除节点,并存入到`+寄存器` 
  `pp` 从`+寄存器` 粘贴节点到当前节点之后.
## voomlog 
  创建`__pylog__` 缓冲区,可以把python 的标准输出和标准输入指定到vim 缓冲区.
  
```vim
  Examples for Python 3:
    :Voomlog
    :py3 assert 2==3
    :py3 print(u"\u042D \u042E \u042F")
    :py3 import this
```
  这对py3 命令调试很有用.
  关闭: :bun, :bd, or :bw .

---
  快速使用手册到此结束.
