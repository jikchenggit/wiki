---
title: 安装要求
description: 安装要求
published: true
date: 2021-08-23T03:56:45.643Z
tags: 
editor: markdown
dateCreated: 2020-12-20T05:52:20.467Z
---

## 16.2. 要求

一般说来，一个现代的与 Unix 兼容的平台应该就能运行PostgreSQL。 到发布为止已经明确测试过的平台的列表在 [第 16.6 节](supported-platforms.html)中进行了描述。

编译PostgreSQL需要下列软件包：

- 要求GNU make版本3.80或以上；其他的make程序或更老的GNU make版本将***不会\***工作（GNU make有时以名字`gmake`安装）。要测试GNU make可以输入：

  ```
  make --version
  ```

  

- 你需要一个ISO/ANSI C 编译器（至少是 C99兼容的）。我们推荐使用最近版本的GCC，不过，众所周知的是PostgreSQL可以利用许多不同厂商的不同编译器进行编译。

- 除了gzip和bzip2之外，我们还需要tar来解包源代码发布。

- 默认时将自动使用GNU Readline库。它允许psql（PostgreSQL的命令行 SQL 解释器）记住你输入的每一个命令并且允许你使用箭头键来找回和编辑之前的命令。如果你不想用它，那么你必需给`configure`声明`--without-readline`选项。作为一种可选方案，你常常可以使用 BSD许可证的`libedit`库，它最初是在NetBSD上开发的。`libedit`库是GNU Readline兼容的， 如果没有发现`libreadline`或者`configure`使用了`--with-libedit-preferred`选项，都会使用这个库。如果你使用的是一个基于包的 Linux 发布，那么要注意你需要`readline`和`readline-devel`两个包，特别是如果这两个包在你的版本里是分开的时候。

- 默认的时候将使用zlib压缩库。 如果你不想使用它，那么你必须给`configure`声明`--without-zlib`选项。使用这个选项关闭了在pg_dump和pg_restore中对压缩归档的支持。



下列包是可选的。在默认配置的时候并不要求它们，但是如果打开了一些编译选项之后就需要它们了，如下文所解释的：

- 要编译服务器端编程语言PL/Perl，你需要一个完整的 Perl安装，包括`libperl` 库和头文件。 所需的最低版本是Perl 5.8.3。 因为PL/Perl是一个共享库， `libperl`库在大多数平台上也必须是一个共享库。最近的版本的 Perl好像已经默认这样做了，但是早先的版本可不是 这样的，而且这总是一种在站点上安装 Perl 的选择。如果选择了编译 PL/Perl但是它却找不到一个共享的 `libperl`，那么`configure`将会失败。 在这种情况下，你将不得不重新手工编译并且安装Perl 以便能编译PL/Perl。在 Perl的配置处理过程中，需要一个共享库。

  如果你想更多地使用PL/Perl， 你应当保证Perl安装在编译时启用了 `usemultiplicity`选项（`perl -V`将会显示是否是这样）。

- 要编译PL/Python服务器端编程语言， 你需要一个Python的安装，包括头文件和distutils模块。最低的版本要求是Python 2.4。如果是版本3.1或更高版本，则支持Python 3， 如果使用 Python 3 请参考[第 45.1 节](plpython-python23.html)

  因为PL/Python将以共享库的方式编译， `libpython`库在大多数平台上也必须是一个共享库。 在默认的从源码安装Python时不是这样的， 而是在很多操作系统发布中有一个共享库可用。如果选择了编译 PL/Python但找不到一个共享的 `libpython`，`configure`将 会失败。这可能意味着你不得不安装额外的包或者（部分）重编译 Python安装以提供这个共享库。 在从源码编译时，请用`--enable-shared`标志运行 Python的配置脚本。

- 如果你想编译PL/Tcl过程语言， 你当然需要安装Tcl，要求的最低版本是 Tcl 8.4。

- 要打开本地语言支持（NLS），也就是说， 用英语之外的语言显示程序的消息，你需要一个Gettext API的实现。有些操作系统内置了这些（例如Linux、NetBSD、Solaris）， 对于其它系统，你可以从http://www.gnu.org/software/gettext/下载一个额外的包。如果你在使用GNU C 库里面的Gettext实现， 那么你就额外需要GNU Gettext包，因为我们需要里面的几个工具程序。对于任何其它的实现，你应该不需要它。

- 如果您想支持加密的客户端连接，则需要OpenSSL，在没有`/dev/urandom`的平台上(Windows除外)，还需要 OpenSSL作为随机数生成器。最低的版本要求是0.9.8。

- 如果你想支持使用Kerberos、OpenLDAP和/或PAM服务的认证，那你需要相应的包。

- 要编译PostgreSQL文档，有一些独立的要求集，请见 [第 J.2 节](docguide-toolsets.html)。



如果你正从Git树而不是使用发布的源代码包进行编译，或者你想做服务器端开发， 那么你还需要下面的包：

- 如果你需要从 Git 检出中编译，或者你修改了实际的扫描器和分析器的定义文件， 那么你需要 GNU Flex和Bison。 如果你需要它们，那么确保自己拿到的是Flex 2.5.31 或更新的版本， 以及Bison 1.875 或者更新的版本。不能使用其他lex和yacc程序。
- 如果需要从 Git 检出中编译，或者你修改了任何使用 Perl 脚本的编译步骤的输入文件，那么你需要Perl 5.8.3或以后的版本。如果你在 Windows 上编译，你在任何情况下都需要Perl。运行一些测试套件时也需要Perl。



如果你需要获取GNU包，你可以在你的本地GNU镜像站点 （看看 https://www.gnu.org/prep/ftp或[ftp://ftp.gnu.org/gnu/](ftp://ftp.gnu.org/gnu/)找到它们。

还要检查一下你是否有足够的磁盘空间。你将大概需要近 100MB 用于存放编译过程中的源码树和大约 20 MB 用于安装目录。 一个空数据库集簇大概需要 35 MB。一个数据库所占的空间大约是存储同样数据的平面文件所占空间的五倍。如果你要运行回归测试，还临时需要额外的 150MB。请用`df`命令检查剩余磁盘空间。