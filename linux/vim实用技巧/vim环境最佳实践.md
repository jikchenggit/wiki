---
title: vim环境最佳实践
description: vim环境最佳实践
published: true
date: 2021-08-23T03:50:21.635Z
tags: vim环境最佳实践
editor: markdown
dateCreated: 2020-09-25T15:07:40.052Z
---

# vim环境最佳实践
## 安装插件管理器
```console
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```
## 设置vimrc文件
```vim
" vim 官方配置
" 1.设置<TAB> 的格式.
set shiftwidth=4 
set softtabstop=4
" 2. 设置补全方式
set wildmode=longest,list
" 3. 保留命令的历史长度.
set history=2000
" 4. map 映射键
cnoremap <C-p> <Up>
cnoremap <C-n> <Down>

" 5.设置不兼容模式
set nocompatible              " be iMproved, required
" 6.打开自动文件高亮
filetype on                  " required
" 7. 设置缓存区的是否能够被隐藏.
"
set hidden
" 8. 设置打开文件时的快捷路径
cnoremap <expr> %% getcmdtype( ) == ':' ? expand('%:h').'/' : '%%'
" 9.跳转增强插件
runtime macros/matchit.vim
" 10.设置常用shell
set shell=/bin/bash

" Vbundle 插件 Plugin  管理插件
" :h Vundle

set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
call vundle#end()            " required
filetype plugin indent on    " required
Plugin 'VundleVim/Vundle.vim'
```
