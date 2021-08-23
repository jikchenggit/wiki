---
title: kill 命令
description: kill 命令
published: true
date: 2021-08-23T03:25:57.842Z
tags: 命令, kill
editor: markdown
dateCreated: 2020-03-20T03:42:35.908Z
---

# kill命令
## kill 信号量
**Linux进程信号**

| 信号 |  名称  |            描述             |
| ---- | ------ | --------------------------- |
| `1`  | `HUP`  | 挂起                        |
| `2`  | `INT`  | 中断                        |
| `3`  | `QUIT` | 结束运行                    |
| `9`  | `KILL` | 无条件终止                   |
| `11` | `SEGV` | 段错误                      |
| `15` | `TERM` | 尽可能终止                   |
| `17` | `STOP` | 无条件停止运行，但不终止      |
| `18` | `TSTP` | 停止或暂停，但继续在后台运行 |
| `19` | `CONT` | 在`STOP`或`TSTP`之后恢复执行 |

kill命令可通过进程ID（PID）给进程发信号。默认情况下，kill命令会向命令行中列出的全部PID发送一个TERM信号。遗憾的是，你只能用进程的PID而不能用命令名，所以kill命令有时并不好用。

要发送进程信号，你必须是进程的属主或登录为root用户。

```bash
$ kill 3940
-bash: kill: (3940) - Operation not permitted
$
```
TERM信号告诉进程可能的话就停止运行。不过，如果有不服管教的进程，那它通常会忽略这个请求。如果要强制终止，-s参数支持指定其他信号（用信号名或信号值）。

你能从下例中看出，kill命令不会有任何输出。
```bash
# kill -s HUP 3940
#
```
要检查kill命令是否有效，可再运行ps或top命令，看看问题进程是否已停止