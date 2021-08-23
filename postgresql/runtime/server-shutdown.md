---
title: 关闭数据库服务器
description: 关闭数据库服务器
published: true
date: 2021-08-23T03:58:33.738Z
tags: 
editor: markdown
dateCreated: 2020-12-21T14:22:50.235Z
---

# 18.5. 关闭服务器



有几种关闭数据库服务器的方法。通过给`postgres`进程发送不同的信号，你就可以控制关闭类型。

- SIGTERM

  这是*智能关闭*模式。在接收SIGTERM后， 服务器将不允许新连接，但是会让现有的会话正常结束它们的工作。仅当所有的会话终止后它才关闭。 如果服务器处在线备份模式，它将等待直到在线备份模式不再被激活。当在线备份模式被激活时， 仍然允许新的连接，但是只能是超级用户的连接（这一例外允许超级用户连接来终止在线备份模式）。 如果服务器在恢复时请求智能关闭，恢复和流复制只有在所有正常会话都终止后才停止。

- SIGINT

  这是*快速关闭*模式。服务器不再允许新的连接，并向所有现有服务器进程发送SIGTERM，让它们中断当前事务并立刻退出。然后服务器等待所有服务器进程退出并最终关闭。 如果服务处于在线备份模式，备份模式将被终止并致使备份无用。

- SIGQUIT

  这是*立即关闭*模式。服务器将给所有子进程发送 SIGQUIT并且等待它们终止。如果有任何进程没有在 5 秒内终止，它们将被发送 SIGKILL。主服务器进程将在所有子进程退出之后立刻退出，而无需做普通的数据库关闭处理。这将导致在下一次启动时（通过重放 WAL 日志）恢复。只在紧急 时才推荐这种方式。



[pg_ctl](app-pg-ctl.html)程序提供了一个发送这些信号关闭服务器的方便的接口。 另外，你在非 Windows 系统上可以用`kill`直接发送这些信号。可以用`ps`程序或者从数据目录的`postmaster.pid`文件中找到`postgres`进程的PID。例如，要做一次快速关闭：

```
$ kill -INT `head -1 /usr/local/pgsql/data/postmaster.pid`
```

## kill 的所有信号量
```
[root@postgres ~]# kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```
## 重要

最好不要使用SIGKILL关闭服务器。这样做将会阻止服务器释放共享内存和信号量。 此外，使用SIGKILL杀掉`postgres`进程时，`postgres`不会有机会将信号传播到它的子进程，所以可能也必须手工杀掉单个的子进程。

要终止单个会话同时允许其他会话继续，使用`pg_terminate_backend()`（参阅[表 9.83](functions-admin.html#FUNCTIONS-ADMIN-SIGNAL-TABLE)） 或发送SIGTERM信号到该会话相关的子进程。

