---
title: voom安装
description: voom安装
published: true
date: 2021-08-23T03:33:04.632Z
tags: voom安装
editor: markdown
dateCreated: 2020-07-02T02:10:58.325Z
---

# Vundle 方式安装
1. [安装Vundle](/zh/linux/vim实用技巧/Vundle插件管理器安装).
2. 添加以下代码到`.vimrc`文件中
```vim
""""""""""""""" markdown """""""""""""""""""""""""'
Plugin 'vim-voom/VOoM'

"1. 根据文件类型进行自动窗口打开
let g:voom_ft_modes = {'markdown': 'markdown', 'tex': 'latex'}
```
3. 打开`vim` ,执行`PluginIstall` 
4. 安装完毕