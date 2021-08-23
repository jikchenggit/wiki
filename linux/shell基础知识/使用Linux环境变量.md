---
title: 使用Linux环境变量
description: 使用Linux环境变量,
published: true
date: 2021-08-23T03:28:47.710Z
tags: linux, 环境变量
editor: markdown
dateCreated: 2020-04-02T13:34:07.288Z
---

# Linux 环境变量
Linux环境变量能帮你提升Linux shell体验。很多程序和脚本都通过环境变量来获取系统信息、存储临时数据和配置信息。在Linux系统上有很多地方可以设置环境变量，了解去哪里设置相应的环境变量很重要。

本章将带你逐步了解Linux环境变量：它们存储在哪里，怎样使用，以及怎样创建自己的环境变量。最后以数组变量的用法作结。
## 什么是环境变量
bash shell用一个叫作环境变量（environment variable）的特性来存储有关shell会话和工作环境的信息（这也是它们被称作环境变量的原因）。这项特性允许你在内存中存储数据，以便程序或shell中运行的脚本能够轻松访问到它们。这也是存储持久数据的一种简便方法。

在bash shell中，环境变量分为两类：

全局变量

局部变量

本节将描述以上环境变量，并演示怎么查看和使用它们。

> 说明　尽管bash shell使用一致的专有环境变量，但不同的Linux发行版经常会添加其自有的环境变量。你在本章中看到的环境变量的例子可能会跟你安装的发行版中看到的结果略微不同。如果遇到本书未讲到的环境变量，可以查看你的Linux发行版上的文档。

### 全局环境变量
全局环境变量对于shell会话和所有生成的子shell都是可见的。局部变量则只对创建它们的shell可见。这让全局环境变量对那些所创建的子shell需要获取父shell信息的程序来说非常有用。

Linux系统在你开始bash会话时就设置了一些全局环境变量（如想了解此时设置了哪些变量，请参见6.6节）。系统环境变量基本上都是使用全大写字母，以区别于普通用户的环境变量。

要查看全局变量，可以使用env或printenv命令。
```bash
$ printenv
HOSTNAME=server01.class.edu
SELINUX_ROLE_REQUESTED=
TERM=xterm
SHELL=/bin/bash
HISTSIZE=1000
[...]
HOME=/home/Christine
LOGNAME=Christine
[...]
G_BROKEN_FILENAMES=1
_=/usr/bin/printenv
```
要显示个别环境变量的值，可以使用printenv命令，但是不要用env命令。
```bash
$ printenv HOME
/home/Christine
$
$ env HOME
env: HOME: No such file or directory
$
````
也可以使用echo显示变量的值。在这种情况下引用某个环境变量的时候，必须在变量前面加上一个美元符（$）
```bash
$ echo $HOME
/home/Christine
$
```
### 局部环境变量
顾名思义，局部环境变量只能在定义它们的进程中可见。尽管它们是局部的，但是和全局环境变量一样重要。事实上，Linux系统也默认定义了标准的局部环境变量。不过你也可以定义自己的局部变量，如你所想，这些变量被称为用户定义局部变量。

查看局部环境变量的列表有点复杂。遗憾的是，在Linux系统并没有一个只显示局部环境变量的命令。set命令会显示为某个特定进程设置的所有环境变量，包括局部变量、全局变量以及用户定义变量。
```bash
$ set
BASH=/bin/bash
[...]
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_LINENO=()
BASH_SOURCE=()
[...]
colors=/etc/DIR_COLORS
my_variable='Hello World'
[...]
$
````
可以看到，所有通过printenv命令能看到的全局环境变量都出现在了set命令的输出中。但在set命令的输出中还有其他一些环境变量，即局部环境变量和用户定义变量。

> 说明　命令env、printenv和set之间的差异很细微。set命令会显示出全局变量、局部变量以及用户定义变量。它还会按照字母顺序对结果进行排序。env和printenv命令同set命令的区别在于前两个命令不会对变量排序，也不会输出局部变量和用户定义变量。在这种情况下，env和printenv的输出是重复的。不过env命令有一个printenv没有的功能，这使得它要更有用一些。

## 设置用户自定义变量
可以在bash shell中直接设置自己的变量。本节将介绍怎样在交互式shell或shell脚本程序中创建自己的变量并引用它们
### 设置自定义局部变量
一旦启动了bash shell（或者执行一个shell脚本），就能创建在这个shell进程内可见的局部变量了。可以通过等号给环境变量赋值，值可以是数值或字符串。
```bash
$ echo $my_variable

$ my_variable=Hello
$
$ echo $my_variable
Hello
```

如何为有空格的字符串进行赋值
```bash
$ my_variable=Hello World
-bash: World: command not found
$
$ my_variable="Hello World"
$
$ echo $my_variable
Hello World
$
```
> 单引号也可行 `my_variable='Hello World'` 
### 设置自定义全局变量
在设定全局环境变量的进程所创建的子进程中，该变量都是可见的。创建全局环境变量的方法是先创建一个局部环境变量，然后再把它导出到全局环境中。

这个过程通过export命令来完成，变量名前面不需要加$。

```bash
[root@dbserver ~]# my_variable="Hello World"
[root@dbserver ~]# export my_variable
[root@dbserver ~]# echo $my_variable
Hello World
[root@dbserver ~]# bash
[root@dbserver ~]# echo $my_variable
Hello World
[root@dbserver ~]# exit
exit
[root@dbserver ~]# echo $my_variable
Hello World
[root@dbserver ~]# 
```

```bash
[root@dbserver ~]# my_variable="Hello World"
[root@dbserver ~]# export my_variable
[root@dbserver ~]# echo $my_variable
Hello World
[root@dbserver ~]# bash
[root@dbserver ~]#  echo $my_variable
Hello World
[root@dbserver ~]# my_variable="Hello World1"
[root@dbserver ~]# echo $my_variable
Hello World1
[root@dbserver ~]# export my_variable
[root@dbserver ~]# echo $my_variable
Hello World1
[root@dbserver ~]# exit
exit
[root@dbserver ~]# echo $my_variable
Hello World
[root@dbserver ~]# 
```
在定义并导出变量my_variable后，bash命令启动了一个子shell。在这个子shell中能够正确显示出全局环境变量my_variable的值。子shell随后改变了这个变量的值。但是这种改变仅在子shell中有效，并不会被反映到父shell中。

子shell甚至无法使用export命令改变父shell中全局环境变量的值。
尽管子shell重新定义并导出了变量my_variable，但父shell中的my_variable变量依然保留着原先的值。
> 针对这样的现象是子继承父的环境变量,子的全局环境变量并不反馈回父.
如下图所示

@startuml
父bash --|> 子bash
@enduml

## 清除环境变量
当然，既然可以创建新的环境变量，自然也能删除已经存在的环境变量。可以用unset命令完成这个操作。在unset命令中引用环境变量时，记住不要使用$。
```bash
$ echo $my_variable
I am Global now
$
$ unset my_variable
$
$ echo $my_variable
```
# 环境变量生效顺序
当你登录Linux系统时，bash shell会作为登录shell启动。登录shell会从5个不同的启动文件里读取命令：


/etc/profile

$HOME/.bash_profile

$HOME/.bashrc

$HOME/.bash_login

$HOME/.profile

这两个发行版的/etc/profile文件都用到了同一个特性：for语句。它用来迭代/etc/profile.d目录下的所有文件。（该语句会在第13章中详述。）这为Linux系统提供了一个放置特定应用程序启动文件的地方，当用户登录时，shell会执行这些文件。在本书所用的Ubuntu Linux系统中，/etc/profile.d目录下包含以下文件
```bash
[root@dbserver ~]# ls /etc/profile.d/
256term.csh                   colorgrep.csh  csh.local   less.csh     PackageKit.sh          sh.local              vte.sh
256term.sh                    colorgrep.sh   flatpak.sh  less.sh      qt-graphicssystem.csh  vim.csh               which2.csh
abrt-console-notification.sh  colorls.csh    lang.csh    modules.csh  qt-graphicssystem.sh   vim.sh                which2.sh
bash_completion.sh            colorls.sh     lang.sh     modules.sh   rvm.sh                 virtualenvwrapper.sh
```
不难发现，有些文件与系统中的特定应用有关。大部分应用都会创建两个启动文件：一个供bash shell使用（使用.sh扩展名），一个供c shell使用（使用.csh扩展名）。

lang.csh和lang.sh文件会尝试去判定系统上所采用的默认语言字符集，然后设置对应的LANG环境变量。

## 用户级启动文件
剩下的启动文件都起着同一个作用：提供一个用户专属的启动文件来定义该用户所用到的环境变量。大多数Linux发行版只用这四个启动文件中的一到两个：
```bash
$HOME/.bash_profile
$HOME/.bashrc
$HOME/.bash_login
$HOME/.profile
```
shell会按照按照下列顺序，运行第一个被找到的文件，余下的则被忽略：
```bash
$HOME/.bash_profile
$HOME/.bash_login
$HOME/.profile
```

# 环境变量持久化

现在你已经了解了各种shell进程以及对应的环境文件，找出永久性环境变量就容易多了。也可以利用这些文件创建自己的永久性全局变量或局部变量。

对全局环境变量来说（Linux系统中所有用户都需要使用的变量），可能更倾向于将新的或修改过的变量设置放在/etc/profile文件中，但这可不是什么好主意。如果你升级了所用的发行版，这个文件也会跟着更新，那你所有定制过的变量设置可就都没有了。

最好是在/etc/profile.d目录中创建一个以.sh结尾的文件。把所有新的或修改过的全局环境变量设置放在这个文件中。

在大多数发行版中，存储个人用户永久性bash shell变量的地方是$HOME/.bashrc文件。这一点适用于所有类型的shell进程。但如果设置了BASH_ENV变量，那么记住，除非它指向的是$HOME/.bashrc，否则你应该将非交互式shell的用户变量放在别的地方。

> 说明　图形化界面组成部分（如GUI客户端）的环境变量可能需要在另外一些配置文件中设置，这和设置bash shell环境变量的地方不一样。

想想第5章中讲过的alias命令设置就是不能持久的。你可以把自己的alias设置放在$HOME/.bashrc启动文件中，使其效果永久化.
# 数组变量
环境变量有一个很酷的特性就是，它们可作为数组使用。数组是能够存储多个值的变量。这些值可以单独引用，也可以作为整个数组来引用。

要给某个环境变量设置多个值，可以把值放在括号里，值与值之间用空格分隔。
```bash
$ mytest=(one two three four five)
$
```
没什么特别的地方。如果你想把数组像普通的环境变量那样显示，你会失望的。
```bash
$ echo $mytest
one
$
```
只有数组的第一个值显示出来了。要引用一个单独的数组元素，就必须用代表它在数组中位置的数值索引值。索引值要用方括号括起来。
```bash
$ echo ${mytest[2]}
three
$
```
窍门　环境变量数组的索引值都是从零开始。这通常会带来一些困惑。

要显示整个数组变量，可用星号作为通配符放在索引值的位置。
```bash
$ echo ${mytest[*]}
one two three four five
$
```
也可以改变某个索引值位置的值。
```bash
$ mytest[2]=seven
$
$ echo ${mytest[*]}
one two seven four five
$
```
甚至能用unset命令删除数组中的某个值，但是要小心，这可能会有点复杂。看下面的例子。
```bash
$ unset mytest[2]
$
$ echo ${mytest[*]}
one two four five
$
$ echo ${mytest[2]}

$ echo ${mytest[3]}
four
$
```
这个例子用unset命令删除在索引值为2的位置上的值。显示整个数组时，看起来像是索引里面已经没这个索引了。但当专门显示索引值为2的位置上的值时，就能看到这个位置是空的。

最后，可以在unset命令后跟上数组名来删除整个数组。
```bash
$ unset mytest
$
$ echo ${mytest[*]}

$
```
有时数组变量会让事情很麻烦，所以在shell脚本编程时并不常用。对其他shell而言，数组变量的可移植性并不好，如果需要在不同的shell环境下从事大量的脚本编写工作，这会带来很多不便。有些bash系统环境变量使用了数组（比如BASH_VERSINFO），但总体上不会太频繁用到。



