---
title: 内核资源配置
description: 内核资源配置
published: true
date: 2021-08-23T03:58:23.989Z
tags: 
editor: markdown
dateCreated: 2020-12-21T06:45:02.461Z
---

## 18.4. 管理内核资源



PostgreSQL某些时候会耗尽操作系统的各种资源限制，当同一个系统上运行着多个拷贝的服务器或在一个非常大的安装中时尤其如此。本节解释了PostgreSQL使用的内核资源以及你可以采取的用于解决内核资源消耗相关问题的步骤。

### 18.4.1. 共享内存和信号量



PostgreSQL需要操作系统提供进程间通信(IPC)特性， 特别是共享内存和信号量。Unix驱动的系统通常提供 “System V” IPC、 “POSIX” IPC，或者两者都有。 Windows有它自己的这些功能的实现，这里不讨论。

完全缺少这些功能通常表现为服务器启动时的“Illegal system call”错误。这种情况下，除了重新配置内核之外别无选择。PostgreSQL没有它们就不能工作。 不过，在现代操作系统中这种情况是罕见的。

默认情况下，PostgreSQL分配很少量的System V共享内存，和大量的匿名的`mmap` 共享内存。 或者，可以使用单个大型System V共享内存区域。(参见 [shared_memory_type](runtime-config-resource#GUC-SHARED-MEMORY-TYPE))。 另外，在服务器启动时会创建大量信号量，这些信号量可以是System V或POSIX风格。 目前，POSIX信号量用于Linux和FreeBSD系统，而其他平台则使用System V信号量。

### 注意

在PostgreSQL 9.3之前，只使用了System V共享内存， 所以启动服务器所需的System V共享内存的数量更大一些。 如果你在运行着一个老版本的服务器，请参考该服务器版本的文档。

System V IPC特性通常受系统范围分配限制的限制。 当PostgreSQL超出了这些限制之一时，服务器会拒绝启动并且并且留下一条有指导性的错误消息，其中描述了问题以及应该怎么做（又见[第 18.3.1 节](server-start#SERVER-START-FAILURES)）。相关的内核参数在不同系统之间的命名方式一致，[表 18.1](kernel-resources#SYSVIPC-PARAMETERS)给出了一个概述。不过，设置它们的方法却多种多样。下面给出了对于某些平台的建议：

**表 18.1. System V IPC参数**

| 名称     | 描述                               | 运行一个PostgreSQL实例所需的值值                             |
| -------- | ---------------------------------- | ------------------------------------------------------------ |
| `SHMMAX` | 共享内存段的最大尺寸（字节）       | 至少 1kB，但是默认值通常要高一些                             |
| `SHMMIN` | 共享内存段的最小尺寸（字节）       | 1                                                            |
| `SHMALL` | 可用共享内存的总量（字节或页面）   | 如果是字节，同`SHMMAX`；如果是页面， 为`ceil(SHMMAX/PAGE_SIZE)`，加上其他应用程序的空间 |
| `SHMSEG` | 每个进程的最大共享内存段数目       | 只需要 1 段，但是默认值高很多                                |
| `SHMMNI` | 系统范围内的最大共享内存段数目     | 像`SHMSEG`外加其他应用的空间                                 |
| `SEMMNI` | 信号量标识符（即，集合）的最大数目 | at least `ceil((max_connections + autovacuum_max_workers + max_wal_senders + max_worker_processes + 5) / 16)` plus room for other applications |
| `SEMMNS` | 系统范围内的最大信号量数目         | `ceil((max_connections + autovacuum_max_workers + max_wal_senders + max_worker_processes + 5) / 16) * 17` plus room for other applications |
| `SEMMSL` | 每个集合中信号量的最大数目         | 至少 17                                                      |
| `SEMMAP` | 信号量映射中的项数                 | 见文本                                                       |
| `SEMVMX` | 信号量的最大值                     | 至少 1000 （默认值常常是 32767，如非必要不要更改）           |



PostgreSQL要求少量字节的 System V 共享内存（在 64 位平台上通常是 48 字节）用于每一个服务器拷贝。在大多数现代操作系统上，这个量很容易得到。 但是，如果你运行了很多个服务器副本，或者显式配置服务器以使用大量 System V 共享内存(参见 [shared_memory_type](runtime-config-resource#GUC-SHARED-MEMORY-TYPE) 和 [dynamic_shared_memory_type](runtime-config-resource#GUC-DYNAMIC-SHARED-MEMORY-TYPE))， 可能需要增加`SHMALL`（系统范围内 System V 共享内存的总量）。注意在很多系统上`SHMALL`是以页面而不是字节来度量。

不太可能出问题的是共享内存段的最小尺寸（`SHMMIN`），对PostgreSQL来说应该最多大约是 32 字节（通常只是1）。而系统范围（`SHMMNI`）或每个进程（`SHMSEG`）的最大共享内存段数目不太可能会导致问题，除非你的系统把它们设成零。

当使用System V信号量时，PostgreSQL对每个允许的连接（[max_connections](runtime-config-connection#GUC-MAX-CONNECTIONS)）、每个允许的自动清理工作者进程（[autovacuum_max_workers](runtime-config-autovacuum#GUC-AUTOVACUUM-MAX-WORKERS)）和每个允许的后台进程（[max_worker_processes](runtime-config-resource#GUC-MAX-WORKER-PROCESSES)）使用一个信号量， 以16个为一个集合。 每个这种集合还包含第 17 个信号量， 其中存储一个“magic number”，以检测和其它应用使用的信号量集合的冲突。 系统里的最大信号量数目是由`SEMMNS`设置的， 因此这个值必须至少和`max_connections`加`autovacuum_max_workers`加`max_wal_senders`加`max_worker_processes`一样大， 并且每 16 个连接外加工作者还要另外加一个（见[表 18.1](kernel-resources#SYSVIPC-PARAMETERS)中的公式）。 参数`SEMMNI` 决定系统中同一时刻可以存在的信号量集合的数目限制。因此这个参数必须至少为`ceil((max_connections + autovacuum_max_workers + max_wal_senders + max_worker_processes + 5) / 16)`。降低允许的连接数目是一种临时的绕开失败（来自函数`semget`）的方法，通常使用让人混乱的措辞“No space left on device”。

在某些情况下可能还有必要增大`SEMMAP`，使之至少与`SEMMNS`相近。如果系统有这个参数(很多系统没有)，这个参数定义信号量资源映射的尺寸，在其中每个连续的可用信号量块都需要一项。 每当一个信号量集合被释放，那么它要么会被加入到该与被释放块相邻的一个现有项，或者它会被注册在一个新映射项中。如果映射被填满，被释放的信号量将丢失（直到重启）。因此信号量空间的碎片时间长了会导致可用的信号量比应有的信号量少。

与“semaphore undo”有关的其他各种设置，如`SEMMNU`和`SEMUME` 不会影响PostgreSQL。

当使用POSIX信号量时，所需的信号量数量与System V相同， 即每个允许的连接([max_connections](runtime-config-connection#GUC-MAX-CONNECTIONS))、允许的自动清理工作进程 ([autovacuum_max_workers](runtime-config-autovacuum#GUC-AUTOVACUUM-MAX-WORKERS))和允许的后台进程 ([max_worker_processes](runtime-config-resource#GUC-MAX-WORKER-PROCESSES))一个信号量。 在首选此选项的平台上，POSIX信号量的数量没有特定的内核限制。

- AIX

  至少到版本 5.1 为止，不再需要对这些参数（例如`SHMMAX`）做任何特殊的配置，这看起来就像是被配置成允许所有内存都被用作共享内存。这是一种通常被用于其他数据库（DB/2）的配置。但是，可能需要修改`/etc/security/limits`中的全局`ulimit`信息，默认的文件尺寸硬限制（`fsize`）和文件数量（`nofiles`）可能太低。

- FreeBSD

  可以使用`sysctl`或`loader`接口来改变默认IPC配置。下列参数可以使用`sysctl`设置：`# **`sysctl kern.ipc.shmall=32768`** # **`sysctl kern.ipc.shmmax=134217728`** `要让这些设置在重启之后也保持，请修改`/etc/sysctl.conf`。对于`sysctl`所关心的来说这些信号量相关的设置都是只读的，但是可以在`/boot/loader.conf`中设置：`kern.ipc.semmni=256 kern.ipc.semmns=512 `修改该配置文件后，需要重启一次让新设置生效。你可能也希望你的内核将System V共享内存锁定在 RAM 中并且防止它被换页到交换分区。这可以使用`sysctl`的设置 `kern.ipc.shm_use_phys`来完成。如果通过启用sysctl的`security.jail.sysvipc_allowed`运行在 FreeBSD jail 中，运行在不同 jail 中的postmaster应当被不同的操作系统用户运行。这可以提高安全性，因为它阻止非 root 用户干涉不同 jail 中的共享内存或信号量，并且它允许 PostgreSQL IPC 清理代码正确地工作（在 FreeBSD 6.0 及其后的版本中，IPC 清理代码不能正确地检测到其他 jail 中的进程，也不能阻止不同 jail 中的 postmaster 运行在相同的端口）。FreeBSD 4.0 之前的版本的工作与旧版OpenBSD相似（见下文）。

- NetBSD

  在NetBSD 5.0 及其后的版本中，IPC 参数可以使用`sysctl`调整。例如：`$ **`sysctl -w kern.ipc.semmni=100`** `要使这些设置在重启后保持，请修改`/etc/sysctl.conf`。作为NetBSD的默认设置，你总是会想 调大`kern.ipc.semmni`和`kern.ipc.semmns`的值，因为他们实在太小了。你可能也希望你的内核将System V 共享内存锁定在 RAM 中并且防止它被换页到交换分区。这可以使用`sysctl`的设置 `kern.ipc.shm_use_phys`来完成。NetBSD 5.0 以前的版本的工作与旧版OpenBSD相似（见下文），除了那些内核参数应该用关键词`options`设置而不是`option`。

- OpenBSD

  在OpenBSD3.3及以后版本，使用`sysctl`命令，IPC参数可以被自动调节，例如：`# **`sysctl kern.seminfo.semmni=100`** `要使这些设置在重启后保持，请修改`/etc/sysctl.conf`。作为OpenBSD的默认配置，你总是会想调大`kern.seminfo.semmni`和`kern.seminfo.semmns`的值，因为他们实在太小了。在较早的OpenBSD 版本中，你需要编译定制化内核来修改这些IPC参数。也要确保`SYSVSHM`和`SYSVSEM`选项为启用状态。(这两项默认都是启用状态。) 下面给出一些内核配置文件中如何设置这些参数的例子：`option        SYSVSHM option        SHMMAXPGS=4096 option        SHMSEG=256 option        SYSVSEM option        SEMMNI=256 option        SEMMNS=512 option        SEMMNU=256 `

- HP-UX

  默认的设置可以满足正常的安装。在HP-UX 10 上，`SEMMNS`的出厂默认值是 128，这可能对大型数据库站点太低。IPC参数可以在Kernel Configuration → Configurable Parameters下的System Administration Manager（SAM）中被设置。当你完成时选择Create A New Kernel。

- Linux

  默认的最大段尺寸是 32 MB，并且默认的最大总尺寸是 2097152 个页面。一个页面几乎总是 4096 字节，除了在使用少见“huge pages”的内核配置中（使用`getconf PAGE_SIZE`来验证）。共享内存尺寸设置可以通过`sysctl`接口来更改。例如，要允许 16 GB：`$ **`sysctl -w kernel.shmmax=17179869184`** $ **`sysctl -w kernel.shmall=4194304`** `另外在重启之间这些设置可以被保存在文件`/etc/sysctl.conf`中。我们强烈推荐这样做。古老的发型可能没有`sysctl`程序，但是可以通过操纵`/proc`文件系统来得到等效的更改：`$ **`echo 17179869184 >/proc/sys/kernel/shmmax`** $ **`echo 4194304 >/proc/sys/kernel/shmall`** `剩下的默认值都被设置得很宽大，并且通常不需要更改。

- macOS

  在 macOS 中配置共享内存的推荐方法是创建一个名为`/etc/sysctl.conf`的文件，其中包含这样的变量赋值：`kern.sysv.shmmax=4194304 kern.sysv.shmmin=1 kern.sysv.shmmni=32 kern.sysv.shmseg=8 kern.sysv.shmall=1024 `注意在某些 macOS 版本中，***所有五个\***共享内存参数必须在`/etc/sysctl.conf`中设置，否则值将会被忽略。注意近期的 macOS 版本会忽略把`SHMMAX`设置成非 4096 倍数值的尝试。在这个平台上，`SHMALL`以 4kB 的页面度量。在更老的 macOS 版本中，你将需要重启来让共享内存参数的更改生效。到了 10.5，可以使用sysctl随时改变除了`SHMMNI`之外的所有参数。但是最好还是通过`/etc/sysctl.conf`来设置你喜欢的值，这样重启之后这些值还能被保持。只有在 macOS 10.3.9 及以后的版本中才遵循`/etc/sysctl.conf`文件。如果你正在使用 10.3.x 之前的发布，你必须编辑文件`/etc/rc`并且在下列命令中改变值：`sysctl -w kern.sysv.shmmax sysctl -w kern.sysv.shmmin sysctl -w kern.sysv.shmmni sysctl -w kern.sysv.shmseg sysctl -w kern.sysv.shmall `注意`/etc/rc`通常会被 macOS 的系统更新所覆盖，因此你应该在每次更新之后重做这些编辑。在 macOS 10.2 及更早的版本中，应该在文件`/System/Library/StartupItems/SystemTuning/SystemTuning`中编辑这些命令。

- Solaris 2.6 至 2.9 (Solaris 6 至 Solaris 9)

  相似的设置可以在`/etc/system`中更改，例如：`set shmsys:shminfo_shmmax=0x2000000 set shmsys:shminfo_shmmin=1 set shmsys:shminfo_shmmni=256 set shmsys:shminfo_shmseg=256 set semsys:seminfo_semmap=256 set semsys:seminfo_semmni=512 set semsys:seminfo_semmns=512 set semsys:seminfo_semmsl=32 `你需要重启来让更改生效。关于更老版本的 Solaris 下的共享内存的信息请见http://sunsite.uakom.sk/sunworldonline/swol-09-1997/swol-09-insidesolaris。

- Solaris 2.10 (Solaris 10) 及以后 OpenSolaris

  在 Solaris 10 及以后的版本以及 OpenSolaris 中，默认的共享内存和信号量设置已经足以应付大部分PostgreSQL应用。Solaris 现在将`SHMMAX`的默认值设置为系统 RAM的四分之一。要进一步调整这个设置，使用与`postgres`用户有关的一个项目设置。例如，以`root`运行下列命令：`projadd -c "PostgreSQL DB User" -K "project.max-shm-memory=(privileged,8GB,deny)" -U postgres -G postgres user.postgres `这个命令增加`user.postgres`项目并且将用于`postgres`用户的最大共享内存设置为 8GB，并且在下次用户登录进来时或重启PostgreSQL（不是重新载入）时生效。上述假定PostgreSQL是由`postgres`组中的`postgres`用户所运行。不需要重新启动服务器。对于将有巨大数量连接的数据库服务器，我们推荐的其他内核设置修改是：`project.max-shm-ids=(priv,32768,deny) project.max-sem-ids=(priv,4096,deny) project.max-msg-ids=(priv,4096,deny) `此外，如果你正在在一个区中运行PostgreSQL，你可能也需要提升该区的资源使用限制。更多关于`projects` 和`prctl`的信息请见*System Administrator's Guide*中的 "Chapter2: Projects and Tasks"。

### 18.4.2. systemd RemoveIPC



如果正在使用systemd，则必须注意IPC资源（共享内存和信号量） 不会被操作系统过早删除。从源代码安装PostgreSQL时，这尤其值得关注。 PostgreSQL发布包的用户不太可能受到影响，因为`postgres`用户通常是作为系统用户创建的。

控制当用户完全退出时是否移除IPC对象。系统用户免除。 此设置在死板的systemd中默认为on， 但某些操作系统分配默认为关闭。

当此设置打开时，典型的观察效果是PostgreSQL服务器使用的信号量对象在明显随机的时间被删除， 从而导致服务器崩溃，并显示日志消息

```
LOG: semctl(1234567890, 0, IPC_RMID, ...) failed: Invalid argument
```

不同类型的IPC对象（共享内存与信号量，System V与POSIX）在systemd 中略有不同，因此可能会发现某些IPC资源不会像其他IPC资源一样被删除。 但依靠这些微妙的差异是不可取的。

“注销用户”可能会作为维护工作的一部分发生，或者当管理员以 `postgres`用户或类似名称登录时手动发生，所以通常难以防止。

什么是“系统用户”是由`/etc/login.defs`中的 `SYS_UID_MAX`设置在systemd编译时确定的。

打包和部署脚本应该小心，通过使用`useradd -r`、 `adduser --system`或等价物来创建`postgres`用户作为系统用户。

或者，如果用户帐户创建不正确或无法更改，建议设置

```
RemoveIPC=no
```

在`/etc/systemd/logind.conf`或其他适当的配置文件中。

### 小心

至少要确保这两件事中的一件，否则PostgreSQL服务器将非常不可靠。

### 18.4.3. 资源限制

Unix类操作系统强制了许多种资源限制，这些限制可能干扰你的PostgreSQL服务器的操作。尤其重要的是对每个用户的进程数目的限制、每个进程打开文件数目的限制以及每个进程可用的内存的限制。这些限制中每个都有一个“硬”限制和一个“软”限制。实际使用的是软限制，但用户可以自己修改成最大为硬限制的数目。而硬限制只能由root用户修改。系统调用`setrlimit`负责设置这些参数。 shell的内建命令`ulimit`（Bourne shells）或`limit`（csh）被用来从命令行控制资源限制。 在 BSD 衍生的系统上，`/etc/login.conf`文件控制在登录期间设置的各种资源限制。详见操作系统文档。相关的参数是`maxproc`、`openfiles`和`datasize`。例如：

```
default:\
...
        :datasize-cur=256M:\
        :maxproc-cur=256:\
        :openfiles-cur=256:\
...
```

（`-cur`是软限制。增加`-max`可设置硬限制）。

内核也可以在某些资源上有系统范围的限制。

- 在Linux上，`/proc/sys/fs/file-max`决定内核可以支持打开的最大文件数。你可以通过往该文件写入一个不同的数值修改此值， 或者通过在`/etc/sysctl.conf`中增加一个赋值来修改。 每个进程的最大打开文件数限制是在编译内核的时候固定的；更多信息请见`/usr/src/linux/Documentation/proc.txt`。



PostgreSQL服务器为每个连接都使用一个进程， 所以你应该至少和允许的连接同样多的进程，再加上系统其它部分所需要的进程数目。 通常这个并不是什么问题，但如果你在一台机器上运行多个服务器，资源使用可能就会紧张。

打开文件的出厂默认限制通常设置为“socially friendly”的值， 它允许许多用户在一台机器上共存，而不会导致不成比例的系统资源使用。 如果你在一台机器上运行许多服务器，这也许就是你想要的，但是在专门的服务器上， 你可能需要提高这个限制。

在另一方面，一些系统允许独立的进程打开非常多的文件；如果不止几个进程这么干，那系统范围的限制就很容易被超过。如果你发现这样的现像， 并且不想修改系统范围的限制，你就可以设置PostgreSQL的 [max_files_per_process](runtime-config-resource#GUC-MAX-FILES-PER-PROCESS)配置参数来限制打开文件数的消耗。

### 18.4.4. Linux 内存过量使用



在 Linux 2.4 及其后的版本中，默认的虚拟内存行为对PostgreSQL不是最优的。由于内核实现内存过量使用的方法，如果PostgreSQL或其它进程的内存要求导致系统用光虚拟内存，那么内核可能会终止PostgreSQL的 postmaster 进程（主服务器进程）。

如果发生了这样的事情，你会看到像下面这样的内核消息（参考你的系统文档和配置，看看在哪里能看到这样的消息）：

```
Out of Memory: Killed process 12345 (postgres).
```

这表明`postgres`进程因为内存压力而被终止了。尽管现有的数据库连接将继续正常运转，但是新的连接将无法被接受。要想恢复，PostgreSQL应该被重启。

一种避免这个问题的方法是在一台你确信其它进程不会耗尽内存的机器上运行PostgreSQL。 如果内存资源紧张，增加操作系统的交换空间可以帮助避免这个问题，因为内存不足（OOM）杀手（即终止进程这种行为）只有当物理内存和交换空间都被用尽时才会被调用。

如果PostgreSQL本身是导致系统内存耗尽的原因，你可以通过改变你的配置来避免该问题。在某些情况中，降低内存相关的配置参数可能有所帮助，特别是[`shared_buffers`](runtime-config-resource#GUC-SHARED-BUFFERS) 和[`work_mem`](runtime-config-resource#GUC-WORK-MEM)两个参数。在其他情况中，允许太多连接到数据库服务器本身也可能导致该问题。在很多情况下，最好减小[`max_connections`](runtime-config-connection#GUC-MAX-CONNECTIONS)并且转而利用外部连接池软件。

在 Linux 2.6 及其后的版本中，可以修改内核的行为，这样它将不会“过量使用”内存。尽管此设置不会阻止[OOM 杀手](https://lwn.net/Articles/104179/)被调用，但它可以显著地降低其可能性并且将因此得到更鲁棒的系统行为。这可以通过用`sysctl`选择严格的过量使用模式来实现：

```
sysctl -w vm.overcommit_memory=2
```

或者在`/etc/sysctl.conf`中放置一个等效的项。你可能还希望修改相关的设置`vm.overcommit_ratio`。 详细信息请参阅内核文档的https://www.kernel.org/doc/Documentation/vm/overcommit-accounting文件。

另一种方法，可以在改变或不改变`vm.overcommit_memory`的情况下使用。它将 postmaster 进程的进程相关的*OOM score adjustment*值设置为`-1000`，从而保证它不会成为 OOM 杀手的目标。 这样做最简单的方法是在 postmaster 的启动脚本中执行

```
echo -1000 > /proc/self/oom_score_adj
```

并且要在调用 postmaster 之前执行。请注意这个动作必须以 root 完成，否则它将不会产生效果。所以一个被 root 拥有的启动脚本是放置这个动作最容易的地方。如果这样做，你还应该在调用 postmaster 之前在启动脚本中设置这些环境变量：

```
export PG_OOM_ADJUST_FILE=/proc/self/oom_score_adj
export PG_OOM_ADJUST_VALUE=0
```

这些设置将导致 postmaster 子进程使用普通的值为零的 OOM score adjustment 运行，所以 OOM 杀手仍能在需要时把它们作为目标。如果你想要子进程用某些其他 OOM score adjustment 值运行，可以为`PG_OOM_ADJUST_VALUE`使用其他的值（`PG_OOM_ADJUST_VALUE`也能被省略，那时它会被默认为零）。如果你没有设置`PG_OOM_ADJUST_FILE`，子进程将使用和 postmaster 相同的 OOM score adjustment 运行，这是不明智的，因为重点是确保 postmaster 具有优先的设置。

更老的 Linux 内核不提供`/proc/self/oom_score_adj`，但是可能有一个具有相同功能的早期版本，它被称为`/proc/self/oom_adj`。这种方式工作起来完全相同，除了禁用值是`-17`而不是`-1000`。

### 注意

有些厂商的 Linux 2.4 内核被报告有着 2.6 过量使用`sysctl`参数的早期版本。不过，在没有相关代码的 2.4 内核里设置`vm.overcommit_memory`为 2 将会让事情更糟。我们推荐你检查一下实际的内核源代码（见文件`mm/mmap.c`中的`vm_enough_memory`函数），验证一下这个是在你的内核中是被支持的， 然后再在 2.4 安装中使用它。文档文件`overcommit-accounting`的存在***不\***能当作是这个特性存在的证明。如果有疑问，请咨询一位内核专家或你的内核厂商。

### 18.4.5. Linux 大页面

当PostgreSQL使用大量连续的内存块时，使用大页面会减少开销， 特别是在使用大[shared_buffers](runtime-config-resource#GUC-SHARED-BUFFERS)时。 要在PostgreSQL中使用此特性，您需要一个包含 `CONFIG_HUGETLBFS=y`和`CONFIG_HUGETLB_PAGE=y`的内核。 您还必须调整内核设置`vm.nr_hugepages`。要估计所需的巨大页面的数量， 请启动PostgreSQL，而不启用巨大页面，并使用 `/proc`文件系统来检查postmaster的匿名共享内存段大小以及系统的巨大页面大小。 这可能看起来像：

```
$ head -1 $PGDATA/postmaster.pid
4170
$ pmap 4170 | awk '/rw-s/ && /zero/ {print $2}'
6490428K
$ grep ^Hugepagesize /proc/meminfo
Hugepagesize:       2048 kB
```

`6490428` / `2048` 大约是`3169.154`，因此在这个示例中你至少需要 `3170`个大页面，我们可以设置：

```
$ sysctl -w vm.nr_hugepages=3170
```

如果机器上的其他程序也需要大页面，则更大的设置将是合适的。 不要忘记将此设置添加到`/etc/sysctl.conf`， 以便在重启后重新应用它。

有时候内核会无法立即分配想要数量的大页面，所以可能有必要重复该命令或者重新启动。 （在重新启动之后，应立即将大部分机器的内存转换为大页面。） 要验证巨大的页面分配情况，请使用：

```
$ grep Huge /proc/meminfo
```



可能还需要赋予数据库服务器的操作系统用户权限，让他能通过sysctl 设置`vm.hugetlb_shm_group`以使用大页面， 和/或赋予使用`ulimit -l`锁定内存的权限。

PostgreSQL中大页面的默认行为是 尽可能使用它们并且在失败时转回到正常页面。要强制使用大页面，你可 以在`postgresql.conf`中把[huge_pages](runtime-config-resource#GUC-HUGE-PAGES)设置成 `on`。注意此设置下如果没有足够的大页面可用， PostgreSQL将会启动失败。

Linux大页面特性的详细描述可见https://www.kernel.org/doc/Documentation/vm/hugetlbpage.txt.