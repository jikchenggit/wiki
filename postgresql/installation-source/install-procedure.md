---
title: 安装过程
description: 安装过程
published: true
date: 2021-08-23T03:57:27.105Z
tags: 
editor: markdown
dateCreated: 2020-12-20T05:56:42.171Z
---

## 16.4. 安装过程

1. **配置**

   

   安装过程的第一步就是为你的系统配置源代码树并选择你喜欢的选项。这个工作是通过运行`configure`脚本实现的，对于默认安装，你只需要简单地输入：

   ```
   ./configure
   ```

   该脚本将运行一些测试来决定一些系统相关的变量， 并检测你的操作系统的特殊设置，并且最后将在编译树中创建一些文件以记录它找到了什么。如果你想保持编译目录的独立，你也可以在一个源码树之外的目录中运行`configure` 。这个过程也被称为一个*VPATH*编译。做法如下：

   ```
   mkdir build_dir
   cd build_dir
   /path/to/source/tree/configure [options go here]
   make
   ```

   

   默认设置将编译服务器和辅助程序，还有只需要 C 编译器的所有客户端程序和接口。默认时所有文件都将安装到`/usr/local/pgsql`。

   你可以通过给出下面的`configure`命令行选项中的一个或更多的选项来自定义编译和安装过程：

   - `--prefix=*`PREFIX`*`

     把所有文件装在目录*`PREFIX`*中而不是`/usr/local/pgsql`中。 实际的文件会安装到数个子目录中；没有一个文件会直接安装到*`PREFIX`*目录里。如果你有特殊需要，你还可以用下面的选项自定义不同的子目录的位置。 不过，如果你把这些设置保留默认，那么安装将是可重定位的，意思是你可以在安装过后移动目录（`man`和`doc`位置不受此影响）。对于可重定位的安装，你可能需要使用`configure`的`--disable-rpath`选项。 还有，你需要告诉操作系统如何找到共享库。

   - `--exec-prefix=*`EXEC-PREFIX`*`

     你可以把体系相关的文件安装到一个不同的前缀下（*`EXEC-PREFIX`*），而不是*`PREFIX`*中设置的地方。 这样做可以比较方便地在不同主机之间共享体系相关的文件。 如果你省略这些，那么*`EXEC-PREFIX`*就会被设置为等于 *`PREFIX`*并且体系相关和体系无关的文件都会安装到同一棵目录树下，这也可能是你想要的。

   - `--bindir=*`DIRECTORY`*`

     为可执行程序指定目录。默认是`*`EXEC-PREFIX`*/bin`， 通常也就是`/usr/local/pgsql/bin`。

   - `--sysconfdir=*`DIRECTORY`*`

     用于各种各样配置文件的目录，默认为`*`PREFIX`*/etc`。

   - `--libdir=*`DIRECTORY`*`

     设置安装库和动态装载模块的目录。默认是`*`EXEC-PREFIX`*/lib`。

   - `--includedir=*`DIRECTORY`*`

     C 和 C++ 头文件的目录。默认是`*`PREFIX`*/include`。

   - `--datarootdir=*`DIRECTORY`*`

     设置多种只读数据文件的根目录。这只为后面的某些选项设置默认值。默认值为`*`PREFIX`*/share`。

   - `--datadir=*`DIRECTORY`*`

     设置被安装的程序使用的只读数据文件的目录。默认值为`*`DATAROOTDIR`*`。注意这不会对你的数据库文件被放置的位置产生任何影响。

   - `--localedir=*`DIRECTORY`*`

     设置安装区域数据的目录，特别是消息翻译目录文件。默认值为`*`DATAROOTDIR`*/locale`。

   - `--mandir=*`DIRECTORY`*`

     PostgreSQL自带的手册页将安装到这个目录，它们被安装在相应的`man*`x`*`子目录里。 默认是`*`DATAROOTDIR`*/man`。

   - `--docdir=*`DIRECTORY`*`

     设置安装文档文件的根目录，“man”页不包含在内。这只为后续选项设置默认值。这个选项的默认值为`*`DATAROOTDIR`*/doc/postgresql`。

   - `--htmldir=*`DIRECTORY`*`

     PostgreSQL的HTML格式的文档将被安装在这个目录中。默认值为`*`DATAROOTDIR`*`。

   

   ### 注意

   为了让PostgreSQL能够安装在一些共享的安装位置（例如`/usr/local/include`）， 同时又不至于和系统其它部分产生名字空间干扰，我们特别做了一些处理。 首先，安装脚本会自动给`datadir`、`sysconfdir`和`docdir`后面附加上“`/postgresql`”字符串， 除非展开的完整路径名已经包含字符串“`postgres`”或者“`pgsql`”。 例如，如果你选择`/usr/local`作为前缀， 那么文档将安装在`/usr/local/doc/postgresql`，但如果前缀是`/opt/postgres`， 那么它将被放到`/opt/postgres/doc`。客户接口的公共 C 头文件安装到了`includedir`，并且是名字空间无关的。内部的头文件和服务器头文件都安装在`includedir`下的私有目录中。参考每种接口的文档获取关于如何访问头文件的信息。最后，如果合适，那么也会在`libdir`下创建一个私有的子目录用于动态可装载的模块。

   

   

   - `--with-extra-version=*`STRING`*`

     把*`STRING`*追加到 PostgreSQL 版本号。例如，你可以使用它来标记从未发布的 Git 快照或者包含定制补丁（带有一个如`git describe`标识符之类的额外版本号或者一个分发包发行号）创建的二进制文件。

   - `--with-includes=*`DIRECTORIES`*`

     *`DIRECTORIES`*是一个冒号分隔的目录列表，这些目录将被加入编译器的头文件搜索列表中。 如果你有一些可选的包（例如 GNU Readline）安装在非标准位置， 你就必须使用这个选项，以及可能还有相应的 `--with-libraries`选项。例子：`--with-includes=/opt/gnu/include:/usr/sup/include`.

   - `--with-libraries=*`DIRECTORIES`*`

     *`DIRECTORIES`*是一个冒号分隔的目录列表，这些目录是用于查找库文件的。 如果你有一些包安装在非标准位置，你可能就需要使用这个选项（以及对应的`--with-includes`选项）。例子：`--with-libraries=/opt/gnu/lib:/usr/sup/lib`.

   - `--enable-nls[=*`LANGUAGES`*]`

     打开本地语言支持（NLS），也就是以非英文显示程序消息的能力。*`LANGUAGES`*是一个空格分隔的语言代码列表， 表示你想支持的语言。例如`--enable-nls='de fr'` （你提供的列表和实际支持的列表之间的交集将会自动计算出来）。如果你没有声明一个列表，那么就会安装所有可用的翻译。要使用这个选项，你需要一个Gettext API 的实现。见上文。

   - `--with-pgport=*`NUMBER`*`

     把*`NUMBER`*设置为服务器和客户端的默认端口。默认是 5432。 这个端口可以在以后修改，不过如果你在这里声明，那么服务器和客户端将有相同的编译好了的默认值。这样会非常方便些。 通常选取一个非默认值的理由是你企图在同一台机器上运行多个PostgreSQL服务器。

   - `--with-perl`

     制作PL/Perl服务器端编程语言。

   - `--with-python`

     制作PL/Python服务器端编程语言。

   - `--with-tcl`

     制作PL/Tcl服务器编程语言。

   - `--with-tclconfig=*`DIRECTORY`*`

     Tcl 安装文件`tclConfig.sh`，其中里面包含编译与 Tcl 接口的模块的配置信息。该文件通常可以自动地在一个众所周知的位置找到，但是如果你需要一个不同版本的 Tcl，你也可以指定可以找到它的目录。

   - `--with-gssapi`

     编译 GSSAPI 认证支持。在很多系统上，GSSAPI（通常是 Kerberos 安装的一部分）系统不会被安装在默认搜索位置（例如`/usr/include`、`/usr/lib`），因此你必须使用选项`--with-includes`和`--with-libraries`来配合该选项。`configure`将会检查所需的头文件和库以确保你的 GSSAPI 安装足以让配置继续下去。

   - `--with-krb-srvnam=*`NAME`*`

     默认的 Kerberos 服务主的名称（也被 GSSAPI 使用）。默认是`postgres`。通常没有理由改变这个值，除非你是一个 Windows 环境，这种情况下该名称必须被设置为大写形式`POSTGRES`。

   - `--with-llvm`

     支持基于LLVM的JIT编译 （请参阅[第 31 章](jit)）。 这需要安装LLVM库。 当前LLVM的最低要求版本是3.9。`llvm-config` 可用于查找所需的编译选项。`llvm-config`会在`PATH` 上搜索所有受支持版本的`llvm-config-$major-$minor`。 如果那还不能找到正确的二进制文件，请使用`LLVM_CONFIG`指定正确的 `llvm-config`的路径。例如：`./configure ... --with-llvm LLVM_CONFIG='/path/to/llvm/bin/llvm-config' `LLVM支持需要兼容的`clang`编译器 （必要时使用`CLANG`环境变量指定）和有效的C++编译器 （必要时使用使用`CXX`环境变量指定）。

   - `--with-icu`

     支持ICU库。 这需要安装ICU4C软件包。 目前要求的最低ICU4C版本是4.2。默认的，pkg-config 将被用来查找所需的编译选项。支持ICU4C版本4.6及更高版本。 对于较老版本，或者如果pkg-config不可用， 可以将变量`ICU_CFLAGS`和`ICU_LIBS` 指定为`configure`，就像下面的示例中那样：`./configure ... --with-icu ICU_CFLAGS='-I/some/where/include' ICU_LIBS='-L/some/where/lib -licui18n -licuuc -licudata' `(如果ICU4C在编译器的默认搜索路径中， 那么你仍然需要指定一个非空的字符串，以避免使用pkg-config， 例如`ICU_CFLAGS=' '`。)

   - `--with-openssl`

     编译SSL（加密）连接支持。这个选项需要安装OpenSSL包。`configure`将会检查所需的头文件和库以确保你的 OpenSSL安装足以让配置继续下去。

   - `--with-pam`

     编译PAM（可插拔认证模块）支持。

   - `--with-bsd-auth`

     编译 BSD 认证支持（BSD 认证框架目前只在 OpenBSD 上可用）。

   - `--with-ldap`

     为认证和连接参数查找编译LDAP支持 （详见[第 33.17 节](libpq-ldap)和[第 20.10 节](auth-ldap)）。在 Unix 上，这需要安装OpenLDAP包。在 Windows 上将使用默认的WinLDAP库。`configure`将会检查所需的头文件和库以确保你的 OpenLDAP安装足以让配置继续下去。

   - `--with-systemd`

     编译对systemd 服务通知的支持。如果服务器是在systemd 机制下被启动，这可以提高集成度，否则不会有影响 否则，; 参考 [第 18.3 节](server-start) 查看更多信息 。要使用这个选项，必须安装libsystemd 以及相关的头文件。

   - `--without-readline`

     避免使用Readline库（以及libedit）。这个选项禁用了psql中的命令行编辑和历史， 因此我们不建议这么做。

   - `--with-libedit-preferred`

     更倾向于使用BSD许可证的libedit库而不是GPL许可证的Readline。这个选项只有在你同时安装了两个库时才有意义，在那种情况下默认会使用Readline。

   - `--with-bonjour`

     编译 Bonjour 支持。这要求你的操作系统支持 Bonjour。在 macOS 上建议使用。

   - `--with-uuid=*`LIBRARY`*`

     使用指定的 UUID 库编译[uuid-ossp](uuid-ossp)模块（提供生成 UUID 的函数）。 *`LIBRARY`*必须是下列之一：`bsd`，用来使用 FreeBSD、NetBSD 和一些其他 BSD 衍生系统 中的 UUID 函数`e2fs`，用来使用`e2fsprogs`项目创建的 UUID 库， 这个库出现在大部分的 Linux 系统和 macOS 中，并且也能找到用于其他平台的 版本`ossp`，用来使用[OSSP UUID library](http://www.ossp.org/pkg/lib/uuid/)

   - `--with-ossp-uuid`

     `--with-uuid=ossp`的已废弃的等效选项。

   - `--with-libxml`

     编译 libxml （启用 SQL/XML 支持）。这个特性需要 Libxml 版本 2.6.23 及以上。Libxml 会安装一个程序`xml2-config`，它可以被用来检测所需的编译器和链接器选项。如果能找到，PostgreSQL 将自动使用它。要制定一个非常用的 libxml 安装位置，你可以设置环境变量`XML2_CONFIG`指向`xml2-config`程序所属的安装，或者使用选项`--with-includes`和`--with-libraries`。

   - `--with-libxslt`

     编译[xml2](xml2)模块时使用 libxslt。xml2依赖这个库来执行XML的XSL转换。

   - `--disable-float4-byval`

     禁用 float4 值的“传值”，导致它们只能被“传引用”。这个选项会损失性能，但是在需要兼容使用 C 编写并使用“version 0”调用规范的老用户定义函数时可能需要这个选项。更好的长久解决方案是将任何这样的函数更新成使用“version 1”调用规范。

   - `--disable-float8-byval`

     禁用 float8 值的“传值”，导致它们只能被“传引用”。这个选项会损失性能，但是在需要兼容使用 C 编写并使用“version 0”调用规范的老用户定义函数时可能需要这个选项。更好的长久解决方案是将任何这样的函数更新成使用“version 1”调用规范。注意这个选项并非只影响 float8，它还影响 int8 和某些相关类型如时间戳。在32位平台上，`--disable-float8-byval`是默认选项并且不允许选择`--enable-float8-byval`。

   - `--with-segsize=*`SEGSIZE`*`

     设置*段尺寸*，以 G 字节计。大型的表会被分解成多个操作系统文件，每一个的尺寸等于段尺寸。这避免了与操作系统对文件大小限制相关的问题。默认的段尺寸（1G字节）在所有支持的平台上都是安全的。如果你的操作系统有“largefile”支持（如今大部分都支持），你可以使用一个更大的段尺寸。这可以有助于在使用非常大的表时消耗的文件描述符数目。但是要当心不能选择一个超过你将使用的平台和文件系统所支持尺寸的值。你可能希望使用的其他工具（如tar）也可以对可用文件尺寸设限。如非绝对必要，我们推荐这个值应为2的幂。注意改变这个值需要一次 initdb。

   - `--with-blocksize=*`BLOCKSIZE`*`

     设置*块尺寸*，以 K 字节计。这是表内存储和I/O的单位。默认值（8K字节）适合于大多数情况，但是在特殊情况下可能其他值更有用。这个值必须是2的幂并且在 1 和 32 （K字节）之间。注意修改这个值需要一次 initdb。

   - `--with-wal-blocksize=*`BLOCKSIZE`*`

     设置*WAL 块尺寸*，以 K 字节计。这是 WAL 日志存储和I/O的单位。默认值（8K 字节）适合于大多数情况，但是在特殊情况下其他值更好有用。这个值必须是2的幂并且在 1 到 64（K字节）之间。注意修改这个值需要一次 initdb。

   - `--disable-spinlocks`

     即便PostgreSQL对于该平台没有 CPU 自旋锁支持，也允许编译成功。自旋锁支持的缺乏会导致较差的性能，因此这个选项只有当编译终端或者通知你该平台缺乏自旋锁支持时才应被使用。如果在你的平台上要求使用该选项来编译PostgreSQL，请将此问题报告给PostgreSQL的开发者。

   - `--disable-thread-safety`

     禁用客户端库的线程安全性。这会阻止libpq和ECPG程序中的并发线程安全地控制它们私有的连接句柄。

   - `--with-system-tzdata=*`DIRECTORY`*`

     PostgreSQL包含它自己的时区数据库，它被用于日期和时间操作。这个时区数据库实际上是和 IANA 时区数据库相兼容的，后者在很多操作系统如 FreeBSD、Linux和Solaris上都有提供，因此再次安装它可能是冗余的。当这个选项被使用时，将不会使用*`DIRECTORY`*中系统提供的时区数据库，而是使用包括在 PostgreSQL 源码发布中的时区数据库。*`DIRECTORY`*必须被指定为一个绝对路径。`/usr/share/zoneinfo`在某些操作系统上是一个很有可能的路径。注意安装例程将不会检测不匹配或错误的时区数据。如果你使用这个选项，建议你运行回归测试来验证你指定的时区数据能正常地工作在PostgreSQL中。这个选项主要针对那些很了解他们的目标操作系统的二进制包发布者。使用这个选项主要优点是不管何时当众多本地夏令时规则之一改变时， PostgreSQL 包不需要被升级。另一个优点是如果时区数据库文件在安装时不需要被编译， PostgreSQL 可以被更直接地交叉编译。

   - `--without-zlib`

     避免使用Zlib库。这样就禁用了pg_dump和 pg_restore中对压缩归档的支持。这个选项只适用于那些没有这个库的少见的系统。

   - `--enable-debug`

     把所有程序和库以带有调试符号的方式编译。这意味着你可以通过一个调试器运行程序来分析问题。 这样做显著增大了最后安装的可执行文件的大小，并且在非 GCC 的编译器上，这么做通常还要关闭编译器优化， 这些都导致速度的下降。但是，如果有这些符号的话，就可以非常有效地帮助定位可能发生问题的位置。目前，我们只是在你使用 GCC 的情况下才建议在生产安装中使用这个选项。但是如果你正在进行开发工作，或者正在使用 beta 版本，那么你就应该总是打开它。

   - `--enable-coverage`

     如果在使用 GCC，所有程序和库都会用代码覆盖率测试工具编译。在运行时，它们会在编译目录中生成代码覆盖率度量的文件。详见[第 32.5 节](regress-coverage)。这个选项只用于 GCC 以及做开发工作时。

   - `--enable-profiling`

     如果在使用 GCC，所有程序和库都被编译成可以进行性能分析。在后端退出时，将会创建一个子目录，其中包含用于性能分析的`gmon.out`文件。这个选项只用于 GCC 和做开发工作时。

   - `--enable-cassert`

     打开在服务器中的*assertion*检查， 它会检查许多“不可能发生”的条件。它对于代码开发的用途而言是无价之宝， 不过这些测试可能会显著地降低服务器的速度。并且，打开这个测试不会提高你的系统的稳定性！ 这些断言检查并不是按照严重性分类的，因此一些相对无害的小故障也可能导致服务器重启 — 只要它触发了一次断言失败。 目前，我们不推荐在生产环境中使用这个选项，但是如果你在做开发或者在使用 beta 版本的时候应该打开它。

   - `--enable-depend`

     打开自动倚赖性跟踪。如果打开这个选项，那么制作文件（makefile）将设置为在任何头文件被修改的时候都将重新编译所有受影响的目标文件。 如果你在做开发的工作，那么这个选项很有用，但是如果你只是想编译一次并且安装，那么这就是浪费时间。 目前，这个选项只对 GCC 有用。

   - `--enable-dtrace`

     为PostgreSQL编译对动态跟踪工具 DTrace 的支持。 详见[第 27.5 节](dynamic-trace)。要指向`dtrace`程序，必须设置环境变量`DTRACE`。这通常是必需的，因为`dtrace`通常被安装在`/usr/sbin`中，该路径可能不在搜索路径中。`dtrace`程序的附加命令行选项可以在环境变量`DTRACEFLAGS`中指定。在 Solaris 上，要在一个64位二进制中包括 DTrace，你必须为 configure 指定`DTRACEFLAGS="-64"`。例如，使用 GCC 编译器：`./configure CC='gcc -m64' --enable-dtrace DTRACEFLAGS='-64' ... `使用 Sun 的编译器：`./configure CC='/opt/SUNWspro/bin/cc -xtarget=native64' --enable-dtrace DTRACEFLAGS='-64' ... `

   - `--enable-tap-tests`

     启用 Perl TAP 工具进行测试。这要求安装了 Perl 以及 Perl 模块`IPC::Run`。 详见 [第 32.4 节](regress-tap)。

   

   如果你喜欢用那些和`configure`选取的不同的 C 编译器，那么你可以你的环境变量`CC`设置为你选择的程序。默认时，只要`gcc`可以使用，`configure`将选择它， 或者是该平台的默认（通常是`cc`）。类似地，你可以用`CFLAGS`变量覆盖默认编译器标志。

   你可以在`configure`命令行上指定环境变量， 例如：

   ```
   ./configure CC=/opt/bin/gcc CFLAGS='-O2 -pipe'
   ```

   

   下面是可以以这种方式设置的有效变量的列表：

   - `BISON`

     Bison程序

   - `CC`

     C编译器

   - `CFLAGS`

     传递给 C 编译器的选项

   - `CLANG`

     `clang`程序的路径，用于处理使用`-with-llvm` 进行编译时内联的源代码。

   - `CPP`

     C 预处理器

   - `CPPFLAGS`

     传递给 C 预处理器的选项

   - `CXX`

     C++编译器

   - `CXXFLAGS`

     传给C++编译器的选项

   - `DTRACE`

     `dtrace`程序的位置

   - `DTRACEFLAGS`

     传递给`dtrace`程序的选项

   - `FLEX`

     Flex程序

   - `LDFLAGS`

     链接可执行程序或共享库时使用的选项

   - `LDFLAGS_EX`

     只用于链接可执行程序的附加选项

   - `LDFLAGS_SL`

     只用于链接共享库的附加选项

   - `LLVM_CONFIG`

     `llvm-config`程序用于查找 LLVM安装。

   - `MSGFMT`

     用于本地语言支持的`msgfmt`程序

   - `PERL`

     Perl 解释器的全路径名称。这将被用来决定编译 PL/Perl 时的依赖性。

   - `PYTHON`

     Python 解释器程序。这将被用来决定编译 PL/Python 时的依赖性。另外这里指定的是 Python 2 还是 Python 3 （或者是隐式选择）决定了 PL/Python 语言的哪一种变种将成为可用的。 详见[第 45.1 节](plpython-python23)。 如果未设置，则按以下顺序探测：`python python3 python2`。

   - `TCLSH`

     Tcl 解释器的程序。这将被用来决定编译 PL/Tcl 时的依赖性，并且它将被替换到 Tcl 脚本中。

   - `XML2_CONFIG`

     用于定位 libxml 安装的`xml2-config`程序。

   

   有时候，将编译器标志事后添加到由`configure`选择的集合中非常有用。 一个重要的例子是，gcc的`-Werror` 选项不能包含在传递给`configure`的`CFLAGS`中， 因为它会中断许多`configure`的内置测试。要添加这样的标志， 在运行`make`时将它们包含在`COPT`环境变量中。 将`COPT`的内容添加到由`configure`设置的 `CFLAGS`和`LDFLAGS`中。例如，你可以这样做

   ```
   make COPT='-Werror'
   ```

   或者

   ```
   export COPT='-Werror'
   make
   ```

   

   ### 注意

   在开发服务器内部代码时，我们推荐使用配置选项`--enable-cassert`（它会打开很多运行时错误检查）和`--enable-debug`（它会提高调试工具的有用性）。

   如果在使用 GCC，最好使用至少`-O1`的优化级别来编译，因为不使用优化（`-O0`）会禁用某些重要的编译器警告（例如使用未经初始化的变量）。但是，非零的优化级别会使调试更复杂，因为在编译好的代码中步进通常将不能和源代码行一一对应。如果你在尝试调试优化过的代码时觉得困惑，将感兴趣的特定文件使用`-O0`编译。一种简单的方式是传递一个选项给make：`make PROFILE=-O0 file.o`。

   `COPT`和`PROFILE`环境变量同样由PostgreSQL makefile实际处理。要使用哪个是一个性能问题，但是开发者的共同习惯是将 `PROFILE`用于一次性的标识调整，而始终保持设置`COPT`。

2. **编译**

   要开始编译，键入：

   ```
   make
   make all
   ```

   （一定要记得用GNU make）。依你的硬件而异，编译过程可能需要 5 分钟到半小时。显示的最后一行应该是：

   ```
   All of PostgreSQL successfully made. Ready to install.
   ```

   

   如果你希望编译所有能编译的东西，包括文档（HTML和手册页）以及附加模块（`contrib`），这样键入：

   ```
   make world
   ```

   显示的最后一行应该是：

   ```
   PostgreSQL, contrib, and documentation successfully made. Ready to install.
   ```

   

   如果要调用另一个makefile而不是手动构建，则必须取消设置 `MAKELEVEL`或将其设置为零，例如这样:

   ```
   build-postgresql:
           $(MAKE) -C postgresql MAKELEVEL=0 all
   ```

   否则可能会导致奇怪的错误消息，通常是有关缺少头文件的消息。

3. **回归测试**

   

   如果你想在安装文件前测试新编译的服务器， 那么你可以在这个时候运行回归测试。 回归测试是一个用于验证PostgreSQL在你的系统上是否按照开发人员设想的那样运行的测试套件。键入：

   ```
   make check
   ```

   （这条命令不能以 root 运行；请在非特权用户下运行该命令）。 (This won't work as root; do it as an unprivileged user.) 详细参考[第 32 章](regress)中关于如何解释测试结果的信息。你可以在以后的任何时间通过执行这条命令来运行这个测试。

4. **安装文件**

   ### 注意

   如果你正在升级一套现有的系统，请阅读[第 18.6 节](upgrading)。 其中有关于升级一个集簇的指导。

   要安装PostgreSQL，输入：

   ```
   make install
   ```

   这条命令将把文件安装到在[步骤 1](install-procedure#CONFIGURE)中指定的目录。确保你有足够的权限向该区域写入。通常你需要用 root 权限做这一步。或者你也可以事先创建目标目录并且分派合适的权限。

   要安装文档（HTML和手册页），输入：

   ```
   make install-docs
   ```

   

   如果你按照上面的方法编译了所有东西，输入：

   ```
   make install-world
   ```

   这也会安装文档。

   你可以使用`make install-strip`代替`make install`， 在安装可执行文件和库文件时把它们剥离。 这样将节约一些空间。如果你编译时带着调试支持，那么抽取将有效地删除调试支持， 因此我们应该只是在不再需要调试的时候做这些事情。 `install-strip`力图做一些合理的工作来节约空间， 但是它并不了解如何从可执行文件中抽取每个不需要的字节， 因此，如果你希望节约所有可能节约的磁盘空间，那么你可能需要手工做些处理。

   标准的安装只提供客户端应用开发和服务器端程序开发所需的所有头文件，例如用 C 写的定制函数或者数据类型（在PostgreSQL 8.0 之前，后者需要独立地执行一次`make install-all-headers`命令，不过现在这个步骤已经融合到标准的安装步骤中）。

   **只安装客户端：.** 如果你只想装客户应用和接口，那么你可以用下面的命令：

   ```
   make -C src/bin install
   make -C src/include install
   make -C src/interfaces install
   make -C doc install
   ```

   `src/bin`中有一些服务器专用的二进制文件，但是它们很小。

**卸载：.** 要撤销安装可以使用命令`make uninstall`。不过这样不会删除任何创建出来的目录。

**清理：.** 在安装完成以后，你可以通过在源码树里面用命令`make clean`删除编译文件。 这样会保留`configure`程序生成的文件，这样以后你就可以用`make`命令重新编译所有东西。 要把源码树恢复为发布时的状态，可用`make distclean`命令。 如果你想从同一棵源码树上为多个不同平台制作，你就一定要运行这条命令并且为每个编译重新配置（另外一种方法是在每种平台上使用一套独立的编译树，这样源代码树就可以保留不被更改）。

如果你执行了一次制作，然后发现你的`configure`选项是错误的， 或者你修改了任何`configure`所探测的东西（例如，升级了软件）， 那么在重新配置和编译之前运行一下`make distclean`是个好习惯。如果不这样做， 你修改的配置选项可能无法传播到所有需要变化的地方。