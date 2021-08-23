---
title: history 命令
description: history 命令
published: true
date: 2021-08-23T03:28:30.199Z
tags: 命令, history
editor: markdown
dateCreated: 2020-04-02T12:58:10.585Z
---

# history 命令
一个有用的内建命令是history命令。bash shell会跟踪你用过的命令。你可以唤回这些命令并重新使用。

要查看最近用过的命令列表，可以输入不带选项的history命令.
```bash
$ history
    1  ps -f
    2  pwd
    3  ls
    4  coproc ( sleep 10; sleep 2 )
    5  jobs
    6  ps --forest
    7  ls
    8  ps -f
    9  pwd
   10  ls -l /bin/ps
   11  history
   12  cd /etc
   13  pwd
   14  ls
   15  cd
   16  type pwd
   17  which pwd
   18  type echo
   19  which echo
   20  type -a pwd
   21  type -a echo
   22  pwd
   23  history
 ```
窍门　你可以设置保存在bash历史记录中的命令数。要想实现这一点，你需要修改名为HISTSIZE的环境变量（参见第6章）。

你可以唤回并重用历史列表中最近的命令。这样能够节省时间和击键量。输入!!，然后按回车键就能够唤出刚刚用过的那条命令来使用。
```bash
[root@dbserver ~]# echo $HISTSIZE
1000
[root@dbserver ~]# !!
echo $HISTSIZE
1000
[root@dbserver ~]# 
```
命令历史记录被保存在隐藏文件.bash_history中，它位于用户的主目录中。这里要注意的是，bash命令的历史记录是先存放在内存中，当shell退出时才被写入到历史文件中。

可以在退出shell会话之前强制将命令历史记录写入.bash_history文件。要实现强制写入，需要使用history命令的-a选项

```bash
$ history -a
$
$ history
[...]
   25  ps --forest
   26  history
   27  ps --forest
   28  history
   29  ls -a
   30  cat .bash_history
   31  history -a
   32  history
$
$ cat .bash_history
[...]
ps --forest
history
ps --forest
history
ls -a
cat .bash_history
history -a
```
> 说明　如果你打开了多个终端会话，仍然可以使用history -a命令在打开的会话中向.bash_history文件中添加记录。但是对于其他打开的终端会话，历史记录并不会自动更新。这是因为.bash_history文件只有在打开首个终端会话时才会被读取。要想强制重新读取.bash_history文件，更新终端会话的历史记录，可以使用history -n命令。
```bash
$ !20
type -a pwd
pwd is a shell builtin
pwd is /bin/pwd
$
```

编号为20的命令从命令历史记录中被取出。和执行最近的命令一样，bash shell首先显示出从shell历史记录中唤回的命令，然后执行该命令。

使用bash shell命令历史记录能够大大地节省时间。利用内建的history命令能够做到的事情远不止这里所描述的。可以通过输入man history来查看history命令的bash手册页面。
- **以下是history 命令参数**

| 选项 |                                            说明                                             |
| --- | ------------------------------------------------------------------------------------------ |
| -c   | 通过删除所有条目清除历史记录列表.                                                              |
| -d   | 删除位置偏移处的历史记录项。                                                                  |
| -a   | 把内存里还未写入文件的命令历史记录全部写入到文件中。                                             |
| -n   | 当前会话不会重新加载其他会话的命令历史记录,这个选项把尚未从历史文件读取的内容,再进行附加到历史文件中. |
| -r   | 读取当前历史文件的内容,并用作当前内存里的历史记录.                                              |
| -w   | 把当前的历史记录写入到历史文件中去.                                                            |


