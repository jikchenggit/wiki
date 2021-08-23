---
title: 创建一个数据库
description: 创建一个数据库
published: true
date: 2021-08-23T03:57:46.701Z
tags: 
editor: markdown
dateCreated: 2020-12-21T06:11:20.763Z
---

## 第 18 章 服务器设置和操作

**目录**

- [18.1. PostgreSQL用户账户](postgres-user)

- [18.2. 创建一个数据库集簇](creating-cluster)

- [18.3. 启动数据库服务器](server-start)

- [18.4. 管理内核资源](kernel-resources)

- [18.5. 关闭服务器](server-shutdown)

- [18.6. 升级一个PostgreSQL集簇](upgrading)

- [18.6.1. 通过pg_dumpall升级数据](upgrading/UPGRADING-VIA-PGDUMPALL)
  
- [18.6.2. 通过pg_upgrade升级数据](upgrading/UPGRADING-VIA-PG-UPGRADE)

- [18.6.3. 通过复制升级数据](upgrading/UPGRADING-VIA-REPLICATION)

- [18.7. 阻止服务器欺骗](preventing-server-spoofing)

- [18.8. 加密选项](encryption-options)

- [18.9. 用 SSL 进行安全的 TCP/IP 连接](ssl-tcp)

-  [18.9.1. Basic Setup](ssl-tcp/SSL-SETUP)
  
-  [18.9.2. OpenSSL配置](ssl-tcp/SSL-OPENSSL-CONFIG)

- [18.9.3. 使用客户端证书](ssl-tcp/SSL-CLIENT-CERTIFICATES)

- [18.9.4. SSL 服务器文件用法](ssl-tcp/SSL-SERVER-FILES)

- [18.9.5. 创建证书](ssl-tcp/SSL-CERTIFICATE-CREATION)

- [18.10. Secure TCP/IP Connections with GSSAPI Encryption](gssapi-enc)

- [18.10.1. 基本设置](gssapi-enc/GSSAPI-SETUP)

- [18.11. 使用SSH隧道的安全 TCP/IP 连接](ssh-tunnels)

- [18.12. 在Windows上注册事件日志](event-log-registration)

本章讨论如何设置和运行数据库服务器，以及它与操作系统的交互。