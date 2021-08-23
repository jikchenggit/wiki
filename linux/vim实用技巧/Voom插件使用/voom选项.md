---
title: voom选项
description: voom选项
published: true
date: 2021-08-23T03:33:53.898Z
tags: voom选项
editor: markdown
dateCreated: 2020-07-06T14:03:26.812Z
---

# voom 设置环境变量
## python 参数
- g:voom_python_versions  

参数解释: voom 使用python2,还是python3,

总是使用Python 2: 
```vim
let g:voom_python_versions = [2]
```
            
1. 总是使用Python 3: 
```vim
let g:voom_python_versions = [3]
```
2. 首先使用`python3 `,如果`python3` 没有再使用`python2`
```vim
let g:voom_python_versions = [3,2]
```
3. 首先使用`python2`,如果`python2` 没有再使用`python3`
```vim
let g:voom_python_versions = [2,3]
```

> (如果此参数未定义,则按照第三种情况执行) 
## 窗口参数

- g:voom_tree_placement 
大纲视图在:"left", "right", "top", "bottom"   (大纲视图上下左右哪一个位置)
默认值`left`.
```vim
let g:voom_tree_placement = "right"  
```

- g:voom_tree_width
大纲视图的宽度    
默认值: 30 
```vim
let g:voom_tree_width = 40
```
- g:voom_tree_height
大纲视图的高度
默认值: 12
```vim
 let g:voom_tree_height = 15   
```
- g:voom_log_placement 
`__pylog__` 窗口创建的位置("left", "right", "top", "bottom"     )

```vim
let g:voom_log_placement = "top"  
```
- :voom_log_width 
`__pylog__` 窗口宽度
 Default: 30
 ```vim
 let g:voom_log_width = 40
 ```
 - g:voom_log_height 
 `__pylog__` 窗口高度
 ```vim
 let g:voom_log_height = 15
 ```
 ## 默认文件处理模式
 - g:voom_ft_modes  
 根据每个文件后缀名(文件类型)不同,进行不同的标记模式.
 ```vim
  let g:voom_ft_modes = {'markdown': 'markdown', 'tex': 'latex'} 
  ```
  例如:打开一个*.md 文件
  ```vim
  :Voom 
  ```
  相当于
  ```vim
  :Voom makrdown
  ```
- :voom_default_mode 
指定无论打开什么样的文件类型的文件,都指定同一种标记处理模式.
```vim
let g:voom_default_mode = 'asciidoc'
```
> 意: 只能选择一个处理模式.
 例如:打开一个*.* 文件
  ```vim
  :Voom 
  ```
  相当于
  ```vim
  :Voom asciidoc
  ```
  
---
选项介绍到此为止:
如需更多参考请见:`h voom-help`