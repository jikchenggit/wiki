---
title: 理解shell
description: 理解shell
published: true
date: 2021-08-23T03:27:55.577Z
tags: 简介, shell
editor: markdown
dateCreated: 2020-03-30T07:15:07.943Z
---

# 理解shell
# shell 的类型
系统启动什么样的shell程序取决于你个人的用户ID配置。在/etc/passwd文件中，在用户ID记录的第7个字段中列出了默认的shell程序。只要用户登录到某个虚拟控制台终端或是在GUI中启动终端仿真器，默认的shell程序就会开始运行。

在下面的例子中，用户christine使用GNU bash shell作为自己的默认shell程序：
```bash
$ cat /etc/passwd
[...]
Christine:x:501:501:Christine B:/home/Christine:/bin/bash
$
```
bash shell程序位于/bin目录内。从长列表中可以看出/bin/bash（bash shell）是一个可执行程序：
```bash
$ ls -lF /bin/bash
-rwxr-xr-x. 1 root root 938832 Jul 18  2013 /bin/bash*
$
```
CentOS发行版中还有其他一些shell程序。其中包括tcsh，它源自最初的C shell：
```bash
$ ls -lF /bin/tcsh
-rwxr-xr-x. 1 root root 387328 Feb 21  2013 /bin/tcsh*
$
```
另外还包括ash shell的Debian版：
```bash
$ ls -lF /bin/dash
-rwxr-xr-x. 1 root root 109672 Oct 17  2012 /bin/dash*
$
```

## shell 的父子关系
用于登录某个虚拟控制器终端或在GUI中运行终端仿真器时所启动的默认的交互shell，是一个父shell。本书到目前为止都是父shell提供CLI提示符，然后等待命令输入。

在CLI提示符后输入/bin/bash命令或其他等效的bash命令时，会创建一个新的shell程序。这个shell程序被称为子shell（child shell）。子shell也拥有CLI提示符，同样会等待命令输入。

当输入bash、生成子shell的时候，你是看不到任何相关的信息的，因此需要另一条命令帮助我们理清这一切。第4章中讲过的ps命令能够派上用场，在生成子shell的前后配合选项-f来使用。
```bash
$ ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
501       1841  1840  0 11:50 pts/0    00:00:00 -bash
501       2429  1841  4 13:44 pts/0    00:00:00 ps -f
$
$ bash
$
$ ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
501       1841  1840  0 11:50 pts/0    00:00:00 -bash
501       2430  1841  0 13:44 pts/0    00:00:00 bash
501       2444  2430  1 13:44 pts/0    00:00:00 ps -f
$
```

第一次使用ps -f的时候，显示出了两个进程。其中一个进程的进程ID是1841（第二列），运行的是bash shell程序（最后一列）。另一个进程（进程ID为2429）对应的是命令ps -f。

说明　进程就是正在运行的程序。bash shell是一个程序，当它运行的时候，就成为了一个进程。一个运行着的shell就是某种进程而已。因此，在说到运行一个bash shell的时候，你经常会看到“shell”和“进程”这两个词交换使用。

输入命令bash之后，一个子shell就出现了。第二个ps -f是在子shell中执行的。可以从显示结果中看到有两个bash shell程序在运行。第一个bash shell程序，也就是父shell进程，其原始进程ID是1841。第二个bash shell程序，即子shell进程，其PID是2430。注意，子shell的父进程ID（PPID）是1841，指明了这个父shell进程就是该子shell的父进程。图5-1展示了这种关系。

![00016.gif](/00016.gif)

**图 5-1　bash shell进程的父子关系**

在生成子shell进程时，只有部分父进程的环境被复制到子shell环境中。这会对包括变量在内的一些东西造成影响，

子shell（child shell，也叫subshell）可以从父shell中创建，也可以从另一个子shell中创建。


```bash
$ ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
501       1841  1840  0 11:50 pts/0    00:00:00 -bash
501       2532  1841  1 14:22 pts/0    00:00:00 ps -f
$
$ bash
$
$ bash
$
$ bash
$
$ ps --forest
  PID TTY          TIME CMD
 1841 pts/0    00:00:00 bash
 2533 pts/0    00:00:00  \_ bash
 2546 pts/0    00:00:00      \_ bash
 2562 pts/0    00:00:00          \_ bash
 2576 pts/0    00:00:00              \_ ps
$
```

在上面的例子中，bash命令被输入了三次。这实际上创建了三个子shell。ps -forest命令展示了这些子shell间的嵌套结构。图5-2中也展示了这种关系。

![00017.gif](/00017.gif)

**图 5-2　子shell的嵌套关系**

- bash命令行参数

|    参数     |                   描述                   |
| ----------- | ---------------------------------------- |
| `-c string` | 从`string`中读取命令并进行处理             |
| `-i`        | 启动一个能够接收用户输入的交互shell        |
| `-l`        | 以登录shell的形式启动                     |
| `-r`        | 启动一个受限shell，用户会被限制在默认目录中 |
| `-s`        | 从标准输入中读取命令                       |

## 进程列表
你可以在一行中指定要依次运行的一系列命令。这可以通过命令列表来实现，只需要在命令之间加入分号（;）即可。

```bash
$ pwd ; ls ; cd /etc ; pwd ; cd ; pwd ; ls
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
/etc
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
$
```
在上面的例子中，所有的命令依次执行，不存在任何问题。不过这并不是进程列表。命令列表要想成为进程列表，这些命令必须包含在括号里。

```bash
$ (pwd ; ls ; cd /etc ; pwd ; cd ; pwd ; ls)
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
/etc
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
$
```

尽管多出来的括号看起来没有什么太大的不同，但起到的效果确是非同寻常。括号的加入使命令列表变成了进程列表，生成了一个子shell来执行对应的命令。

> 说明　进程列表是一种命令分组（command grouping）。另一种命令分组是将命令放入花括号中，并在命令列表尾部加上分号（;）。语法为{ command; }。使用花括号进行命令分组并不会像进程列表那样创建出子shell。

要想知道是否生成了子shell，得借助一个使用了环境变量的命令。（环境变量会在第6章中详述。）这个命令就是echo $BASH_SUBSHELL。如果该命令返回0，就表明没有子shell。如果返回1或者其他更大的数字，就表明存在子shell。
```bash
$ pwd ; ls ; cd /etc ; pwd ; cd ; pwd ; ls ; echo $BASH_SUBSHELL
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
/etc
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
0
```

在命令输出的最后，显示的是数字0。这就表明这些命令不是在子shell中运行的。

要是使用进程列表的话，结果就不一样了。在列表最后加入echo $BASH_SUBSHELL。


```bash
$ (pwd ; ls ; cd /etc ; pwd ; cd ; pwd ; ls ; echo $BASH_SUBSHELL)
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
/etc
/home/Christine
Desktop    Downloads  Music     Public     Videos
Documents  junk.dat   Pictures  Templates
1
```
这次在命令输入的最后显示出了数字1。这表明的确创建了子shell，并用于执行这些命令。

所以说，命令列表就是使用括号包围起来的一组命令，它能够创建出子shell来执行这些命令。你甚至可以在命令列表中嵌套括号来创建子shell的子shell。
```bash
$ ( pwd ; echo $BASH_SUBSHELL)
/home/Christine
1
$ ( pwd ; (echo $BASH_SUBSHELL))
/home/Christine
2
```
注意，在第一个进程列表中，数字1表明了一个子shell，这个结果和预期的一样。但是在第二个进程列表中，在命令echo $BASH_SUBSHELL外面又多出了一对括号。这对括号在子shell中产生了另一个子shell来执行命令。因此数字2表明的就是这个子shell。
## 后台shell
```bash
$ sleep 3000&
[1] 2396
$ ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
christi+  2338  2337  0 10:13 pts/9    00:00:00 -bash
christi+  2396  2338  0 10:17 pts/9    00:00:00 sleep 3000
christi+  2397  2338  0 10:17 pts/9    00:00:00 ps -f
$
```

sleep命令会在后台（&）睡眠3000秒（50分钟）。当它被置入后台，在shell CLI提示符返回之前，会出现两条信息。第一条信息是显示在方括号中的后台作业（background job）号（1）。第二条是后台作业的进程ID（2396）。

```bash
$ jobs -l
[1]+  2396 Running                 sleep 3000 &
$
```
利用jobs命令的-l（字母L的小写形式）选项，你还能够看到更多的相关信息。除了默认信息之外，-l选项还能够显示出命令的PID。
### 后台程序协程
协程可以同时做两件事。它在后台生成一个子shell，并在这个子shell中执行命令。

要进行协程处理，得使用coproc命令，还有要在子shell中执行的命令。
```bash
$ coproc sleep 10
[1] 2544
$
```
在上面的例子中可以看到在子shell中执行的后台命令是coproc COPROC sleep 10。COPROC是coproc命令给进程起的名字。你可以使用命令的扩展语法自己设置这个名字.
```bash
$ coproc My_Job { sleep 10; }
[1] 2570
$
$ jobs
[1]+  Running                 coproc My_Job { sleep 10; } &
$
```
通过使用扩展语法，协程的名字被设置成My_Job。这里要注意的是，扩展语法写起来有点麻烦。必须确保在第一个花括号（{）和命令名之间有一个空格。还必须保证命令以分号（;）结尾。另外，分号和闭花括号（}）之间也得有一个空格。
> 说明　协程能够让你尽情发挥想象力，发送或接收来自子shell中进程的信息。只有在拥有多个协程的时候才需要对协程进行命名，因为你得和它们进行通信。否则的话，让coproc命令将其设置成默认的名字COPROC就行了
# shell 内建命令
在学习GNU bash shell期间，你可能听到过“内建命令”这个术语。搞明白shell的内建命令和非内建（外部）命令非常重要。内建命令和非内建命令的操作方式大不相同。
## 外部命令
外部命令，有时候也被称为文件系统命令，是存在于bash shell之外的程序。它们并不是shell程序的一部分。外部命令程序通常位于/bin、/usr/bin、/sbin或/usr/sbin中。

ps就是一个外部命令。你可以使用which和type命令找到它
```bash
$ which ps
/bin/ps
$
$ type -a ps
ps is /bin/ps
$
$ ls -l /bin/ps
-rwxr-xr-x 1 root root 93232 Jan  6 18:32 /bin/ps
$
```

当外部命令执行时，会创建出一个子进程。这种操作被称为衍生（forking）。外部命令ps很方便显示出它的父进程以及自己所对应的衍生子进程。
![00018.gif](/00018.gif)

当进程必须执行衍生操作时，它需要花费时间和精力来设置新子进程的环境。所以说，外部命令多少还是有代价的。

说明　就算衍生出子进程或是创建了子shell，你仍然可以通过发送信号与其沟通，这一点无论是在命令行还是在脚本编写中都是极其有用的。发送信号（signaling）使得进程间可以通过信号进行通信。

## 内建命令
内建命令和外部命令的区别在于前者不需要使用子进程来执行。它们已经和shell编译成了一体，作为shell工具的组成部分存在。不需要借助外部程序文件来运行。

cd和exit命令都内建于bash shell。可以利用type命令来了解某个命令是否是内建的。
```bash
$ type cd
cd is a shell builtin
$
$ type exit
exit is a shell builtin
$
```
要注意，有些命令有多种实现。例如echo和pwd既有内建命令也有外部命令。两种实现略有不同。要查看命令的不同实现，使用type命令的-a选项。

> 窍门　对于有多种实现的命令，如果想要使用其外部命令实现，直接指明对应的文件就可以了。例如，要使用外部命令pwd，可以输入/bin/pwd。