---
title: 安装后设置
description: 安装后设置
published: true
date: 2021-08-23T03:57:35.597Z
tags: 
editor: markdown
dateCreated: 2020-12-20T05:59:32.544Z
---

## 16.5. 安装后设置

- [16.5.1. 共享库](install-post#)
- [16.5.2. 环境变量](install-post#id-1.6.3.9.3)

### 16.5.1. 共享库



在一些有共享库的系统里，你需要告诉你的系统如何找到新安装的共享库。那些并***不\***是必须做这个工作的系统包括 FreeBSD、HP-UX、Linux、NetBSD、OpenBSD和Solaris。

设置共享库的搜索路径的方法因平台而异， 但是最广泛使用的方法是设置环境变量`LD_LIBRARY_PATH`，例如在 Bourne shells （`sh`、`ksh`、`bash`、`zsh`）中：

```
LD_LIBRARY_PATH=/usr/local/pgsql/lib
export LD_LIBRARY_PATH
```

或者在`csh`或`tcsh`中：

```
setenv LD_LIBRARY_PATH /usr/local/pgsql/lib
```

把`/usr/local/pgsql/lib`换成你在[步骤 1](install-procedure#CONFIGURE)时设置的`--libdir`。 你应该把这些命令放到 shell 启动文件，如`/etc/profile`或`~/.bash_profile`中。 和这个方法相关的一些注意事项和很好的信息可以在http://xahlee.info/UnixResource_dir/_/ldpath找到。

在有些系统上，更好的方法可能是在编译***之前\***设置环境变量`LD_RUN_PATH`。

在Cygwin上，把库目录放在`PATH`中或者把`.dll`文件移动到`bin`目录。

如果有疑问，请参考你的系统的手册页（可能是`ld.so`或`rld`）。 如果稍后你收到下面这样的消息：

```
psql: error in loading shared libraries
libpq.so.2.1: cannot open shared object file: No such file or directory
```

那么这一步就是必须的了。这个只需关注一下就是了。

如果你用的系统是Linux，并且你还有 root 权限，那么你可以在安装之后运行：

```
/sbin/ldconfig /usr/local/pgsql/lib
```

（或者等效的目录）以便让运行时链接器更快地找到共享库。请参考`ldconfig`的手册页获取更多信息。在FreeBSD、NetBSD和OpenBSD上，命令是：

```
/sbin/ldconfig -m /usr/local/pgsql/lib
```

我们不知道其它的系统有等效的命令。

### 16.5.2. 环境变量



如果你安装到`/usr/local/pgsql`或者其他默认不在搜索路径中的地方， 那你应该在你的`PATH`环境变量里面增加一个 `/usr/local/pgsql/bin`（或者是你在[步骤 1](install-procedure#CONFIGURE)时给选项`--bindir`设置的任何值） 。严格来说，这些都不是必须的，但这么做可以让你使用PostgreSQL更方便。

要做这些事情，把下面几行加到你的 shell 启动文件，如`~/.bash_profile`（如果想影响所有用户就放在`/etc/profile`）：

```
PATH=/usr/local/pgsql/bin:$PATH
export PATH
```

如果你用的是`csh`或者`tcsh`，那么用这条命令：

```
set path = ( /usr/local/pgsql/bin $path )
```



为了让你的系统找得到man文档，你需要加类似下面的一行到一个shell启动文件里 （除非你安装到了默认搜索的位置）：

```
MANPATH=/usr/local/pgsql/share/man:$MANPATH
export MANPATH
```



环境变量`PGHOST`和`PGPORT`为客户端应用指定了数据库服务器的主机和端口， 它们会覆盖编译时的默认项。如果你想从远程运行客户端应用， 那么为每个准备使用该数据库的用户都设置`PGHOST`将会非常方便。但这不是必须的，而且大部分客户端程序也可以通过命令行选项替换这些设置。

### 16.5.2. 环境变量



如果你安装到`/usr/local/pgsql`或者其他默认不在搜索路径中的地方， 那你应该在你的`PATH`环境变量里面增加一个 `/usr/local/pgsql/bin`（或者是你在[步骤 1](install-procedure.html#CONFIGURE)时给选项`--bindir`设置的任何值） 。严格来说，这些都不是必须的，但这么做可以让你使用PostgreSQL更方便。

要做这些事情，把下面几行加到你的 shell 启动文件，如`~/.bash_profile`（如果想影响所有用户就放在`/etc/profile`）：

```
PATH=/usr/local/pgsql/bin:$PATH
export PATH
```

如果你用的是`csh`或者`tcsh`，那么用这条命令：

```
set path = ( /usr/local/pgsql/bin $path )
```



为了让你的系统找得到man文档，你需要加类似下面的一行到一个shell启动文件里 （除非你安装到了默认搜索的位置）：

```
MANPATH=/usr/local/pgsql/share/man:$MANPATH
export MANPATH
```



环境变量`PGHOST`和`PGPORT`为客户端应用指定了数据库服务器的主机和端口， 它们会覆盖编译时的默认项。如果你想从远程运行客户端应用， 那么为每个准备使用该数据库的用户都设置`PGHOST`将会非常方便。但这不是必须的，而且大部分客户端程序也可以通过命令行选项替换这些设置。