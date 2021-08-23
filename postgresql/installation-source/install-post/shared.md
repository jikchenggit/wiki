---
title: 共享库
description: 共享库
published: true
date: 2021-08-23T04:00:46.326Z
tags: 共享库
editor: markdown
dateCreated: 2021-08-22T05:30:44.276Z
---

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

把`/usr/local/pgsql/lib`换成你在[步骤 1](install-procedure.html#CONFIGURE)时设置的`--libdir`。 你应该把这些命令放到 shell 启动文件，如`/etc/profile`或`~/.bash_profile`中。 和这个方法相关的一些注意事项和很好的信息可以在http://xahlee.info/UnixResource_dir/_/ldpath.html找到。

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