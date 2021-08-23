---
title: 平台相关说明
description: 平台相关说明
published: true
date: 2021-08-23T04:01:17.650Z
tags: 
editor: markdown
dateCreated: 2021-08-22T05:50:22.808Z
---

这一节提供了考虑 PostgreSQL 安装和设置的附加平台相关的话题。确保阅读安装指导，特别是[第 16.2 节](install-requirements.html)。 同样，检查关于回归测试结果解释的[第 32 章](regress.html)。

这里没有覆盖的平台不存在平台相关的安装问题。

### 16.7.1. AIX



PostgreSQL 能在 AIX 上工作，但是AIX 6.1以前的版本有各种问题并且不推荐使用。你可以使用 GCC 或本地 IBM 编译器`xlc`。

#### 16.7.1.1. 内存管理

AIX 的特别之处在于它的内存管理。你可能有一个装备有好多个吉字节空闲 RAM 的服务器，但是在运行应用时仍然会得到内存不足或者地址空间错误。一个例子是加载扩展会因为罕见的错误失败。例如，作为 PostgreSQL 安装的拥有者运行：

```
=# CREATE EXTENSION plperl;
ERROR:  could not load library "/opt/dbs/pgsql/lib/plperl.so": A memory address is not in the address space for the process.
```

作为拥有 PostgreSQL 安装的组中的非拥有者运行：

```
=# CREATE EXTENSION plperl;
ERROR:  could not load library "/opt/dbs/pgsql/lib/plperl.so": Bad address
```

另一个例子是 PostgreSQL 服务器日志中的内存不足错误，每次内存分配接近或者超过 256 MB 时都会失败。

所有这些问题的总体成因是服务器进程所用的寻址空间和内存模型。默认情况下，所有在 AIX 上编译的二进制都是32位。这并不依赖于硬件类型或使用的内核。这些32位进程被限制在 4GB 的内存中，并被使用几种模型之一安排成 256 MB 的段。该默认值允许在堆中低于 256 MB，因为它和栈共享一个单独的段。

在`plperl`的例子中，检查你的 umask 和你的 PostgreSQL 安装中的二进制的权限。这个例子中涉及的二进制是32位的并且被用模式 750 而不是 755 安装。由于这种方式的权限设置，只有所有者或拥有组的成员可以载入该库。因为它不是所有人可读的，载入器将该对象放在进程的堆中而不是它应该被放入的共享库段中。

这个问题的“理想的”解决方案是使用 PostgreSQL 的64位编译，但是这不是总是实用的，因为有32位处理器的系统可以编译64位二进制但是却不能运行它。

如果想要一个 32 位二进制，在开始 PostgreSQL 服务器之前将`LDR_CNTRL`设置为`MAXDATA=0x*`n`*0000000`，其中 1 <= n <= 8，并且尝试不同的值以及`postgresql.conf`设置来找一个能让你满意的配置。这种`LDR_CNTRL`的使用告诉 AIX 你希望服务器留出`MAXDATA`字节给堆，以 256 MB 的段分配。当你找到了一个可工作的配置时，`ldedit`可以被用来修改二进制，这样它们默认使用想要的堆尺寸。PostgreSQL 也可以被重新编译，传递`configure LDFLAGS="-Wl,-bmaxdata:0x*`n`*0000000"`来达到相同的效果。

对于一个 64 位编译，设置`OBJECT_MODE`为 64 并且传递`CC="gcc -maix64"`和`LDFLAGS="-Wl,-bbigtoc"`给`configure`（给`xlc`的选项可能不同）。如果你省略`OBJECT_MODE`的输出，你的编译可能会因为链接器错误而失败。当`OBJECT_MODE`被设置时，它告诉 AIX 的编译工具（如`ar`、`as`和`ld`）默认要处理哪些对象类型。

默认情况下，过量使用页面空间的情况可能会发生。不过我们还没有看到过，当进程用尽内存并且出现了过量使用时 AIX 会杀死进程。我们见到过的最接近于此的是 fork 失败，其原因是系统觉得已经没有足够的内存给另一个进程。和 AIX 的很多其他部分一样，如果这成为了一个问题，页面空间分配方法和耗尽内存导致的杀死在系统范围或进程范围是可以配置的。

##### 参考和资源

“[Large Program Support](http://publib.boulder.ibm.com/infocenter/pseries/topic/com.ibm.aix.doc/aixprggd/genprogc/lrg_prg_support.htm)”. *AIX Documentation: General Programming Concepts: Writing and Debugging Programs*.

“[Program Address Space Overview](http://publib.boulder.ibm.com/infocenter/pseries/topic/com.ibm.aix.doc/aixprggd/genprogc/address_space.htm)”. *AIX Documentation: General Programming Concepts: Writing and Debugging Programs*.

“[Performance Overview of the Virtual Memory Manager (VMM)](http://publib.boulder.ibm.com/infocenter/pseries/v5r3/topic/com.ibm.aix.doc/aixbman/prftungd/resmgmt2.htm)”. *AIX Documentation: Performance Management Guide*.

“[Page Space Allocation](http://publib.boulder.ibm.com/infocenter/pseries/v5r3/topic/com.ibm.aix.doc/aixbman/prftungd/memperf7.htm)”. *AIX Documentation: Performance Management Guide*.

“[Paging-space thresholds tuning](http://publib.boulder.ibm.com/infocenter/pseries/v5r3/topic/com.ibm.aix.doc/aixbman/prftungd/memperf6.htm)”. *AIX Documentation: Performance Management Guide*.

*[Developing and Porting C and C++ Applications on AIX](http://www.redbooks.ibm.com/abstracts/sg245674.html?Open)*. IBM Redbook.

### 16.7.2. Cygwin



PostgreSQL 可以使用 Cygwin 来编译，它是用于 Windows 的一个类 Linux 环境，但是这种方法不如原生 Windows 编译见[第 17 章](install-windows.html)）并且我们已经不再推荐在 Cygwin 下运行一个服务器。

在从源代码编译时，按照UNIX风格安装过程进行（即`./configure; make`; 等；只要注意下列 Cygwin 相关的区别：

- 将你的路径设置为使用 Cygwin 的 bin 目录并且把它放在 Windows 工具的前面。这将帮助避免很多编译的问题。

- 不支持`adduser`命令；使用 Windows NT、2000 或 XP 上的用户管理应用来替代。否则，跳过这一步。

- 不支持`su`命令；在 Windows NT、2000 或 XP 上使用 ssh 来模拟 su。否则，跳过这一步。

- 不支持 OpenSSL。

- 为共享内存支持启动`cygserver`。要这样做，输入命令`/usr/sbin/cygserver &`。这个程序在你启动 PostgreSQL 服务器或初始化一个数据集簇（`initdb`）时的任何时刻都需要被运行。默认的`cygserver`配置可能需要被更改（例如增加`SEMMNS`）来防止 PostgreSQL 因为缺少系统资源而失败。

- 在某些不使用 C 区域的系统上编译可能会失败。要修复这个问题，通过在边以前`export LANG=C.utf8`把区域设置为 C，并且在安装完 PostgreSQL 之后把区域恢复成之前的设置。

- 并行回归测试（`make check`）可能产生虚假的回归测试错误，这是由于溢出的`listen()`连接缓冲区，它会导致连接拒绝错误或挂起。你可以使用`MAX_CONNECTIONS`来限制连接数：

  ```
  make MAX_CONNECTIONS=5 check
  ```

  （在某些系统上你可以有大约 10 个同时连接。）



可以把`cygserver` PostgreSQL 服务器安装为 Windows NT 服务。关于如何这样做的信息，请参考包含在 Cygwin 上 PostgreSQL 二进制包中的`README`文档。它被安装在目录`/usr/share/doc/Cygwin`中。

### 16.7.3. macOS



在最新的macOS版本中，有必要将“sysroot” 路径嵌入用于查找某些系统头文件的include选项中。这导致configure 脚本的输出会有所不同，具体取决于在configure期间使用的SDK版本。 在简单的情况下，这应该不会造成任何问题，但是，如果您要尝试在与构建服务器代码不同 的计算机上构建扩展程序，则可能需要强制使用其他sysroot路径。为此，需要设置 `PG_SYSROOT`，例如：

```
make PG_SYSROOT=/desired/path all
```

要在您的计算机上找到合适的路径，请运行

```
xcodebuild -version -sdk macosx Path
```

请注意，实际上不建议使用与构建核心服务器不同的sysroot版本构建扩展。 在最坏的情况下，它可能导致难以调试的ABI不一致。

您还可以在配置时选择非默认的sysroot路径，通过在 configure中指定`PG_SYSROOT`：

```
./configure ... PG_SYSROOT=/desired/path
```



macOS的“系统完整性保护”（SIP） 功能破坏了`make check`，因为它阻止通过 设置所需的`DYLD_LIBRARY_PATH`传递给被测试的可执行文件。 您可以通过在`make check`之前执行`make install`来解决此问题。   不过，大多数PostgreSQL开发人员关闭了SIP。

### 16.7.4. MinGW/原生 Windows



用于 Windows 的 PostgreSQL 可以使用 MinGW 编译，它是一个用于微软操作系统的类 Unix 的编译环境。也可以使用微软的Visual C++编译器套件来编译。 MinGW 编译步骤使用本章中描述的正常编译系统；而 Visual C++ 编译的工作完全不同并且在[第 17 章](install-windows.html)/中描述。

原生 Windows 移植要求一个 Windows 2000 或更高的 32 或 64 位版本。早期的操作系统没有足够的基础设施（但 Cygwin可以用在它们之上）。类 Unix 的编译工具 MinGW 和 MSYS（一个 Unix 工具集合，用于运行如`configure`之类的 shell 脚本）可以从http://www.mingw.org/下载。运行结果二进制两者都需要，它们只在创建二进制时需要。

要使用 MinGW 编译 64 位二进制，从http://mingw-w64.sourceforge.net/安装 64 位工具。把它放在`PATH`中的 bin 目录，并且使用`--host=x86_64-w64-mingw32`选项运行`configure`.

在你安装完所有的东西之后，我们建议你在`CMD.EXE`下运行psql，因为 MSYS 控制台有缓冲问题。

#### 16.7.4.1. 在 Windows 上收集崩溃转储

如果 PostgreSQL 在 Windows 上崩溃，它有能力产生minidumps，这可以被用来追踪崩溃发生的原因，这与 Unix 上的核心转储相似。这些转储可以被使用Windows Debugger Tools或Visual Studio读取。要启用在 Windows 上的转储生成，可在集簇数据目录下创建一个名为`crashdumps`的子目录。转储将被写入到这个目录，转储的名字基于崩溃进程的标识符和崩溃的当前时间来确定。

### 16.7.5. Solaris



PostgreSQL 在 Solaris 上得到了很好的支持。你的操作系统越新，你将会碰到更少的问题。

#### 16.7.5.1. 要求的工具

你可以使用 GCC 或 Sun 的编译器套件进行编译。为了更好的代码优化，我们强烈推荐在 SPARC 架构下使用 Sun 的编译器。如果你正在使用 Sun 的编译器，注意不要选择`/usr/ucb/cc`；而是使用`/opt/SUNWspro/bin/cc`。

你可以从https://www.oracle.com/technetwork/server-storage/solarisstudio/downloads/下载 Sun Studio。很多 GNU 工具都被整合到了 Solaris 10，或者它们在 Solaris companion CD 中。如果你需要用于老版本 Solaris 的包，你可以在[http://www.sunfreeware.com](http://www.sunfreeware.com/)找到这些工具。如果你想要源码，在https://www.gnu.org/prep/ftp上找找。

#### 16.7.5.2. configure 抱怨一个失败的测试程序

如果`configure`抱怨一个失败的测试程序，可能的情况是运行时链接器无法找到某些库，可能是libz、libreadline或某些其他非标准库如 libssl。要向它指出正确的位置，在`configure`命令行上设置`LDFLAGS`环境变量，例如：

```
configure ... LDFLAGS="-R /usr/sfw/lib:/opt/sfw/lib:/usr/local/lib"
```

更多信息可见ld手册页。

#### 16.7.5.3. 为最优性能编译

在 SPARC 架构上，我们强烈推荐使用 Sun Studio来编译。尝试使用`-xO5`优化标志来生成显著加快的二进制。不要使用任何修改浮点操作和`errno`处理（例如`-fast`）行为的标志。

如果你没有理由要使用 SPARC 上的 64 位二进制，最好用 32 位版本。64 位操作较慢并且 64 位二进制比其 32 位变体要慢。在另一方面，AMD64 CPU 家族上的32 位代码不是原生的，所以在那个 CPU 族中 32 位代码要明显地更慢。

#### 16.7.5.4. 用 DTrace 来跟踪 PostgreSQL

是的，可以使用 DTrace。详见[第 27.5 节](dynamic-trace.html)。

如果你看到`postgres`可执行程序的链接中断并且报出下面的错误消息：

```
Undefined                       first referenced
 symbol                             in file
AbortTransaction                    utils/probes.o
CommitTransaction                   utils/probes.o
ld: fatal: Symbol referencing errors. No output written to postgres
collect2: ld returned 1 exit status
make: *** [postgres] Error 1
```

说明你的 DTrace 安装太旧，无法处理静态函数中的探测。你需要 Solaris 10u4 或更新的版本以使用DTrace。