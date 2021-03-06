---
title: top
description: top命令
published: true
date: 2021-08-23T03:25:46.282Z
tags: top, 命令
editor: markdown
dateCreated: 2020-03-20T03:38:15.389Z
---

# 实时检测进程
ps命令虽然在收集运行在系统上的进程信息时非常有用，但也有不足之处：它只能显示某个特定时间点的信息。如果想观察那些频繁换进换出的内存的进程趋势，用ps命令就不方便了。

而top命令刚好适用这种情况。top命令跟ps命令相似，能够显示进程信息，但它是实时显示的。图4-1是top命令运行时输出的截图。
```
top - 10:46:25 up 12 days, 16:32,  1 user,  load average: 0.06, 0.06, 0.06
Tasks: 365 total,   1 running, 364 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.3 us,  5.8 sy,  0.0 ni, 91.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  3780908 total,   256784 free,  2456572 used,  1067552 buff/cache
KiB Swap:  8384508 total,  6647920 free,  1736588 used.  1314188 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                            
 8361 root      20   0  162300   4580   3676 R  15.8  0.1   0:00.07 top                                                                                                                
   25 root      20   0       0      0      0 S   5.3  0.0  49:45.38 rcuos/2                                                                                                            
    1 root      20   0  191660   5256   3496 S   0.0  0.1  11:00.66 systemd                                                                                                            
    2 root      20   0       0      0      0 S   0.0  0.0   0:01.99 kthreadd                                                                                                           
    3 root      20   0       0      0      0 S   0.0  0.0   1:37.19 ksoftirqd/
```

输出的第一部分显示的是系统的概况：第一行显示了当前时间、系统的运行时间、登录的用户数以及系统的平均负载。

平均负载有3个值：最近1分钟的、最近5分钟的和最近15分钟的平均负载。值越大说明系统的负载越高。由于进程短期的突发性活动，出现最近1分钟的高负载值也很常见，但如果近15分钟内的平均负载都很高，就说明系统可能有问题。

> 说明　Linux系统管理的要点在于定义究竟到什么程度才算是高负载。这个值取决于系统的硬件配置以及系统上通常运行的程序。对某个系统来说是高负载的值可能对另一系统来说就是正常值。通常，如果系统的负载值超过了2，就说明系统比较繁忙了.

第二行显示了进程概要信息——top命令的输出中将进程叫作任务（task）：有多少进程处在运行、休眠、停止或是僵化状态（僵化状态是指进程完成了，但父进程没有响应）。

下一行显示了CPU的概要信息。top根据进程的属主（用户还是系统）和进程的状态（运行、空闲还是等待）将CPU利用率分成几类输出。

紧跟其后的两行说明了系统内存的状态。第一行说的是系统的物理内存：总共有多少内存，当前用了多少，还有多少空闲。后一行说的是同样的信息，不过是针对系统交换空间（如果分配了的话）的状态而言的。

最后一部分显示了当前运行中的进程的详细列表，有些列跟ps命令的输出类似。

- USER：进程属主的名字。

- PR：进程的优先级。

- NI：进程的谦让度值。

- VIRT：进程占用的虚拟内存总量。

- RES：进程占用的物理内存总量。

- SHR：进程和其他进程共享的内存总量。

- S：进程的状态（D代表可中断的休眠状态，R代表在运行状态，S代表休眠状态，T代表跟踪状态或停止状态，Z代表僵化状态）。

- %CPU：进程使用的CPU时间比例。

- %MEM：进程使用的内存占可用内存的比例。

- TIME+：自进程启动到目前为止的CPU时间总量。

- COMMAND：进程所对应的命令行名称，也就是启动的程序名

# top 实时运行时的参数
 在top  运行期间可以用`h` 键进行进入到帮助页面.按`f` 键可以进入到需要定制展示信息页面.我们分别讨论以下情况:
 	 ## 帮助页面:
   |            |                                 |
   | ---        | ---                             |
   |Z,B,E,e   | 全局参数:`Z` 是否使用色彩模式;`B` 是否加粗; `E`/`e` 统计内存容量的不同方式.|
  | l,t,m    | 切换摘要信息:`l` 平均负载;`t`任务和cpu 状态;`m` 内存信息.|
  | 0,1,2,3,I | 切换参数: 0,1,2,3,I 显示不同的CPU 性能展示页面.|
  | f,F,X     | 字段:`f`,`F` 添加/删除/固定/排序; `X` 增加固定宽度.
  | L,&,<,> . | 定位参数:`L`/`&` 查找/再次查找; 变更需要对应排序哪个列`<` /`>` |
  |R,H,V,J . |切换参数: `R` 排序,`H`线程;`V` 树状视图; `J`  左对齐右对齐|
  | c,i,S,j . | 切换参数: 'c' 命令行的排序/行; 'i' 显示空闲的进程; 'S' Time; 'j' 左对齐右对齐|
  | x,y     . |切换高亮显示: 'x' 排序字段; 'y' 显示运行的任务.|
  | z,b     . |Toggle: 'z' 高亮颜色/不高亮颜色; 'b' 加粗或高亮字体底色 (只有在 'x' 或 'y'模式下)|
  | u,U,o,O . |Filter by: 'u'/'U' effective/any user; 'o'/'O' other criteria|
  | n,#,^O  . |设置: `n`/`#` 最大任务显示数量; 显示: Ctrl+'O' 其他过滤规则.|
  | C,...   . |切换显示模式使用光标移动显示页面: up,down,left,right,home,end|
  | k,r       | 操控任务: 'k' 杀死进程; 'r' 调整优先级|
  | d or s    | 设置更新间隔.|
  | W,Y       | Write configuration file 'W'; Inspect other output 'Y'|
  | q         | Quit|
     
>( 带有`.` 的命令.需要可一个可见的任务显示窗口) 

   