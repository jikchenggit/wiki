---
title: 源码安装postgresql
description: 源码安装postgresql
published: true
date: 2021-08-23T03:56:28.958Z
tags: 
editor: markdown
dateCreated: 2020-12-20T05:49:22.509Z
---

## 第 16 章 从源代码安装

**目录**

- [16.1. 简单版](install-short)

- [16.2. 要求](install-requirements)

- [16.3. 获取源码](install-getsource)

- [16.4. 安装过程](install-procedure)

- [16.5. 安装后设置](install-post)

- [16.5.1. 共享库](install-post/shared)
  
- [16.5.2. 环境变量](install-post/envariment)

- [16.6. 平台支持](supported-platforms)

- [16.7. 平台相关的说明](installation-platform-notes)

- [16.7.1. AIX](installation-platform-notes-INSTALLATION-NOTES-AIX)
  
- [16.7.2. Cygwin](installation-platform-notes-INSTALLATION-NOTES-CYGWIN)
  
- [16.7.3. macOS](installation-platform-notes-INSTALLATION-NOTES-MACOS)
  
- [16.7.4. MinGW/原生 Windows](installation-platform-notes-INSTALLATION-NOTES-MINGW)
  
- [16.7.5. Solaris](installation-platform-notes-INSTALLATION-NOTES-SOLARIS)



本章的内容描述从源代码发布安装PostgreSQL。如果你安装的是打包好的版本如RPM或Debian包，那么请略过这一章并且阅读打包者的指导。

如果在微软Windows上构建PostgreSQL，如果你打算使用MinGW或Cygwin构建，请阅读本章; 但是如果你打算使用微软的Visual C++构建，请参见[第 17 章](install-windows)。