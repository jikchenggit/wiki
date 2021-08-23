---
title: 平台支持
description: 平台支持
published: true
date: 2021-08-23T04:01:10.078Z
tags: 平台支持
editor: markdown
dateCreated: 2021-08-22T05:47:48.586Z
---

# 平台测试
如果代码包含规定要工作在一个平台（即一种 CPU 架构和操作系统的结合）上并且它最近已经被验证能在该平台上编译并通过其回归测试，PostgreSQL开发社区才会认为该平台是被支持的。目前，大部分平台兼容性的测试都是由[PostgreSQL 编译农场](https://buildfarm.postgresql.org/)的测试机器自动完成的。如果你对在一个并没有出现在编译农场中的平台上运行PostgreSQL感兴趣，但是代码确实能够工作或者能被修改得工作，我们强烈鼓励你建立一个编译农场成员机器，这样进一步的兼容性可以被确认。

通常，PostgreSQL被期望能在这些 CPU 架构上工作：x86、 x86_64、IA64、PowerPC、PowerPC 64、S/390、S/390x、Sparc、Sparc 64、ARM、MIPS、MIPSEL和PA-RISC。存在对 M68K、M32R 和 VAX 的代码支持，但是这些架构上并没有近期测试的报告。通常也可以在一个为支持的 CPU 类型上通过使用`--disable-spinlocks`配置来进行编译，但是性能将会比较差。

PostgreSQL被期望能在这些操作系统上工作： Linux（所有最近的发布）、Windows（Win2000 SP4及以上）、 FreeBSD、OpenBSD、NetBSD、macOS、AIX、HP/UX 和 Solaris。其他类 Unix 系统可能也可以工作，但是目前没有被测试。在大部分情况下，一个给定操作系统所支持的所有 CPU 架构都能工作。查找下文的[第 16.7 节](installation-platform-notes.html)来看是否有与你的操作系统相关的信息，特别是使用一个老的系统时更应该这样做。

如果你在一个平台上有安装问题，并且该平台根据最近的编译农场结果已经可以被支持，请将问题报告给`<pgsql-bugs@lists.postgresql.org>`。如果你有兴趣将PostgreSQL移植到一个新的平台，`<pgsql-hackers@lists.postgresql.org>`是一个合适的讨论它的地方。