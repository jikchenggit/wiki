---
title: emby 
description: emby 一个非官方的影音体验中心
published: true
date: 2021-08-23T03:33:27.201Z
tags: emby
editor: markdown
dateCreated: 2020-07-03T17:51:59.583Z
---

# emby安装
下载方式:
1. 通过白群晖添加[https://synology.emby.media](https://synology.emby.media)第三方源添加.

获取最新版本方式1:
首先利用群晖的日志获取emby的XPEnology版本的套件下载地址：
先正常添加emby的XPEnology套件源，点开始安装，不等下载完成就点取消，然后cat /var/log/messages,会发现类似：
```log
2019-05-08T10:56:11+08:00 ds3617xs synoscgi_SYNO.Core.Package.Installation_1_install[18131]: pkgserver.cpp:261 Failed to read data.
2019-05-08T10:56:11+08:00 ds3617xs synoscgi_SYNO.Core.Package.Installation_1_install[18131]: pkgserver.cpp:651 Failed to curl perform, code=42, err=Error, url=https://synology.emby.media/packages/xpenology/stable/4.1.1.0-1/EmbyServer_4.1.1.0-1_xpenology.spk
2019-05-08T10:56:14+08:00 ds3617xs synostoraged: cache_monitor.c:1622 Can't support DS with cpu number (1)
```
最新版本获取方式2:
查看emby 的最新版本号,更改以下链接,链接具有以下规律:

[https://synology.emby.media/packages/xpenology/stable/4.4.2.0-1/EmbyServer_4.4.2.0-1_xpenology.spk
https://synology.emby.media/packages/xpenology/stable/4.4.3.0-1/EmbyServer_4.4.3.0-1_xpenology.spk](https://synology.emby.media/packages/xpenology/stable/4.4.2.0-1/EmbyServer_4.4.2.0-1_xpenology.spk%0Ahttps://synology.emby.media/packages/xpenology/stable/4.4.3.0-1/EmbyServer_4.4.3.0-1_xpenology.spk)
下载地址:
[https://synology.emby.media/packages/xpenology/stable/4.4.3.0-1/EmbyServer_4.4.3.0-1_xpenology.spk](https://synology.emby.media/packages/xpenology/stable/4.4.3.0-1/EmbyServer_4.4.3.0-1_xpenology.spk)