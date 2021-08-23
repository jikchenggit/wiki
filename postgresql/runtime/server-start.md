---
title: 启动数据库
description: 启动数据库
published: true
date: 2021-08-23T03:58:15.411Z
tags: 
editor: markdown
dateCreated: 2020-12-21T06:42:03.749Z
---

## 18.3. 启动数据库服务器

- [18.3.1. 服务器启动失败](server-start#SERVER-START-FAILURES)
- [18.3.2. 客户端连接问题](server-start#CLIENT-CONNECTION-PROBLEMS)

在任何人可以访问数据库前，你必须启动数据库服务器。 数据库服务器程序是`postgres`， 它必须知道在哪里能找到它要用的数据。这是用`-D`选项实现的。 因此，启动服务器最简单的方法是：

```
$ postgres -D /usr/local/pgsql/data
```

这将把服务器放在前台运行。这个步骤同样必须以PostgreSQL用户帐户登录来操作。如果没有`-D`选项，服务器将尝试使用环境变量`PGDATA`命名的目录。如果这个环境变量也没有提供则导致失败。

通常最好在后台启动`postgres`。要这样做，使用常用的 Unix shell 语法：

```
$ postgres -D /usr/local/pgsql/data >logfile 2>&1 &
```

如上所示，把服务器的stdout和stderr输出存储到某个地方是非常重要的。这将对审计目的和诊断问题有所帮助（更深入的有关日志文件处理的讨论请见（[第 24.3 节](logfile-maintenance)）。

`postgres`还接受其它一些命令行选项。更多的信息请见[postgres](app-postgres)参考页 和下面的[第 19 章](runtime-config)。

这些 shell 语法很容易让人觉得无聊。因此我们提供了包装器程序[pg_ctl](app-pg-ctl)以简化一些任务。例如：

```
pg_ctl start -l logfile
```

将在后台启动服务器并且把输出放到指定的日志文件中。`-D`选项和`postgres`中的一样。`pg_ctl`还可以用于停止服务器。

通常，你会希望在计算机启动的时候启动数据库服务器。自动启动脚本是操作系统相关的。PostgreSQL在`contrib/start-scripts`目录中提供了几种。安装将需要 root 权限。

不同的系统在引导时有不同的启动守护进程的习惯。许多系统有一个文件`/etc/rc.local`或`/etc/rc.d/rc.local`。其他的使用`init.d`或`rc.d`目录。不管你做什么，服务器必须由PostgreSQL用户账户***而不是 root\***或任何其他用户启动。因此你可能应该在你的命令中使用`su postgres -c '...'`这种形式。例如：

```
su postgres -c 'pg_ctl start -D /usr/local/pgsql/data -l serverlog'
```



下面是一些更加与操作系统相关的建议（在每一种情况中要确保在我们展示通用值的地方使用正确的安装目录和用户名）。

- 对于FreeBSD，找找PostgreSQL源码发布中的文件`contrib/start-scripts/freebsd`。

- 在OpenBSD上， 把下面几行加到`/etc/rc.local`文件中：

  ```
  if [ -x /usr/local/pgsql/bin/pg_ctl -a -x /usr/local/pgsql/bin/postgres ]; then
      su -l postgres -c '/usr/local/pgsql/bin/pg_ctl start -s -l /var/postgresql/log -D /usr/local/pgsql/data'
      echo -n ' postgresql'
  fi
  ```

  

- 在Linux系统上将

  ```
  /usr/local/pgsql/bin/pg_ctl start -l logfile -D /usr/local/pgsql/data
  ```

  加入到`/etc/rc.d/rc.local`或`/etc/rc.local`中，还可以在PostgreSQL的源码发布中找找文件`contrib/start-scripts/linux`。

  在使用systemd时，可以使用下面的服务单元文件（例如`/etc/systemd/system/postgresql.service`）：

  ```
  [Unit]
  Description=PostgreSQL database server
  Documentation=man:postgres(1)
  
  [Service]
  Type=notify
  User=postgres
  ExecStart=/usr/local/pgsql/bin/postgres -D /usr/local/pgsql/data
  ExecReload=/bin/kill -HUP $MAINPID
  KillMode=mixed
  KillSignal=SIGINT
  TimeoutSec=0
  
  [Install]
  WantedBy=multi-user.target
  ```

  使用`Type=notify`要求服务器的二进制文件使用`configure --with-systemd`编译。

  要仔细地考虑超时设置。在写作这份文档时，systemd的默认超时时长是 90 秒，并且将会杀死没有在这段时间内报告准备好的进程。但是PostgreSQL服务器可能因为执行崩溃恢复而导致启动过程大大超过这个默认时间。建议的值是 0 禁用超时逻辑。

- 在NetBSD上，你可以根据爱好选择FreeBSD或Linux的启动脚本。

- 在Solaris上，创建一个名为`/etc/init.d/postgresql`的文件，其中包含下列行：

  ```
  su - postgres -c "/usr/local/pgsql/bin/pg_ctl start -l logfile -D /usr/local/pgsql/data"
  ```

  然后在`/etc/rc3.d`中创建一个符号链接`S99postgresql`指向它。



当服务器在运行时，它的PID被保存在数据目录中的`postmaster.pid`文件。这样做 可以防止多个服务器实例运行在同一个数据目录中，并且也可以被用来关闭服务器。

### 18.3.1. 服务器启动失败

有几个常见的原因会导致服务器启动失败。通过检查服务器日志或使用手工启动的方法（不做标准输出或标准错误的重定向）， 就可以看到出现什么错误消息。下面我们详细地解释一些最常见的错误消息。



```
LOG:  could not bind IPv4 address "127.0.0.1": Address already in use
HINT:  Is another postmaster already running on port 5432? If not, wait a few seconds and retry.
FATAL:  could not create any TCP/IP sockets
```

正如这个消息所说的，这表示：你试图在一个已经有服务器运行着的端口上再启动另一个服务器。不过，如果核心错误消息不是`Address already in use`或其变体，那就有可能是别的问题。 例如，试图在一个被保留的端口上启动服务器会收到下面这样的消息：

```
$ postgres -p 666
LOG:  could not bind IPv4 address "127.0.0.1": Permission denied
HINT:  Is another postmaster already running on port 666? If not, wait a few seconds and retry.
FATAL:  could not create any TCP/IP sockets
```



像这样的消息：

```
FATAL:  could not create shared memory segment: Invalid argument
DETAIL:  Failed system call was shmget(key=5440001, size=4011376640, 03600).
```

可能意味着你的内核对共享内存区的限制小于PostgreSQL试图创建的工作区域（本例中是 4011376640 字节）。或者可能意味着根本就没有 System-V 风格的共享内存支持被配置在你的内核中。作为一种临时的解决方案， 你可以试着以小于正常数量的缓冲区（[shared_buffers](runtime-config-resource#GUC-SHARED-BUFFERS)）启动服务器。 你最终还是会希望重新配置内核以增加共享内存允许的尺寸。 当你试图在同一台机器上启动多个服务器，并且它们所需的总空间超过了内核的限制，也会报这个错。

一个这样的错误：

```
FATAL:  could not create semaphores: No space left on device
DETAIL:  Failed system call was semget(5440126, 17, 03600).
```

并***不\***意味着你已经用光了磁盘空间。它的意思是你的内核对System V信号量的限制小于PostgreSQL想创建的数量。和上面一样，你可以通过减少允许的连接数（[max_connections](runtime-config-connection#GUC-MAX-CONNECTIONS)）来绕开这个限制，但最终你还是会希望提高内核的限制。

如果你收到一个“illegal system call”错误， 那么很有可能是你的内核根本不支持共享内存或者信号量。这种情况下你唯一的选择就是重新配置内核并且把这些特性打开。

关于配置System V IPC功能的细节请见[第 18.4.1 节](kernel-resources#SYSVIPC)。

### 18.3.2. 客户端连接问题

尽管可能在客户端出现的错误情况范围宽广而且是应用相关的，但的确有几种与服务器的启动方式直接相关。除了下面提到的几种错误之外的问题都应该在相应的客户端应用文档中。



```
psql: could not connect to server: Connection refused
        Is the server running on host "server.joe.com" and accepting
        TCP/IP connections on port 5432?
```

这是常见的“I couldn't find a server to talk to”失败。上面的情况看起来是发生在尝试 TCP/IP 通信时。常见的错误是忘记把服务器配置成允许 TCP/IP 连接。

另外，当试图通过 Unix 域套接字与本地服务器通信时，你会看到这个：

```
psql: could not connect to server: No such file or directory
        Is the server running locally and accepting
        connections on Unix domain socket "/tmp/.s.PGSQL.5432"?
```



最后一行可以验证客户端是不是尝试连接到正确的位置。如果实际上没有服务器在那里运行，典型的核心错误消息将是`Connection refused`或`No such file or directory`（值得注意的是这种环境中的`Connection refused`并***不\***表示服务器得到了你的连接请求并拒绝了它。那种情况会产生一个不同的消息，如[第 20.15 节](client-authentication-problems)中所示）。其它像`Connection timed out`这样的消息可能表示更基础的问题，如缺少网络连接。