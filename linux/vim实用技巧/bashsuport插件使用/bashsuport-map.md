---
title: bashsuport-map
description: bashsuport-map
published: true
date: 2021-08-23T03:34:33.658Z
tags: bashsuport-map
editor: markdown
dateCreated: 2020-07-09T15:05:36.600Z
---

# 注释
| Comments (cont) |                              |        |
| --------------- | ---------------------------- | ------ |
| \css           | script sections              | (n, i) |
| \ckc           | keyword comments             | (n, i) |
| \cma           | plug-in macros               | (n, i) |
| \ce            | echo "actual line"           | (n, i) |
| \cr            | remove echo from actual line | (n, i) |
| \[n\]\\cl | end-of-line comment                   |
| \[n\]\\cj | adjust end-of-line comments (n, i, v) |
| \\cs      | set end-of-line comment col.          |
| \[n\]\\cc | code → comment                        |
| \[n\]\\co | uncomment code                        |
| \\cfr     | frame comment                         |
| \\cfu     | function description                  |
| \\ch      | file header                           |
| \\cd      | date                                  |
| \\ct      | date & time                           |

# 语句类型
| Statements | | |
| --- | --- | --- |
| \sc | case in ... esac | (n, i) |
| \sei | elif then | (n, i) |
| \sf | for in do done | (n, i, v) |
| \sfo | for ((...)) do done | (n, i, v) |
| \si | if then fi | (n, i, v) |
| \sie | if then else fi | (n, i, v) |
| \ss | select in do done | (n, i, v) |
| \su | until do done | (n, i, v) |
| \sw | while do done | (n, i, v) |
| \sfu | function | (n, i, v) |
| \se | echo -e "" | (n, i, v) |
| \sp | printf "%s" | (n, i, v) |
| \sae | array element ${.\[.\]} | (n, i, v) |
| \saa | arr. elem.s (all) ${.\[@\]} | (n, i, v) |
| \sas | arr. elem.s (1 word) ${.\[~*~\]} (n, i, v) |
| \ssa | subarray ${.\[@\]::} | (n, i, v) |
| \san | no. of arr. elem.s ${.\[@\]} | (n, i, v) |
| \sai | list of indices ${.\[~*~\]} | (n, i, v) |

#  测试
| Tests |                                  |        |
| ----- | -------------------------------- | ------ |
| \ta  | arithmetic tests                 | (n, i) |
| \tfp | file permissions                 | (n, i) |
| \tft | file types                       | (n, i) |
| \tfc | file characteristics             | (n, i) |
| \ts  | string comparisons               | (n, i) |
| \toe | option is enabled                | (n, i) |
| \tvs | variables has been set           | (n, i) |
| \tfd | file descr. refers to a terminal | (n, i) |
| \tm  | string matches regexp            | (n, i) |

# I/O 重定向
| IO-Redirection |                               |
| -------------- | ----------------------------- |
| \ior          | IO-redirections (list) (n, i) |
| \ioh          | here-document (n, i)          |

# 正则表达式匹配
| Pattern Matching |                                   |
| ---------------- | --------------------------------- |
| \pzo              | zero or one, ?(                   |
| \pzm              | zero or more, ~*~(                |
| \pom              | one or more, +(                   |
| \peo              | exactly one, @(                   |
| \pae              | anything except, !(               |
| \ppc              | POSIX classes (n, i)              |
| \pbr              | ${BASH_REMATCH\[0. . .3\]} (n, i) |

# 代码片段
| Snippets |                                |
| -------- | ------------------------------ |
| \nr     | read code snippet (n, i)       |
| \nv     | view code snippet (n, i)       |
| \nw     | write code snippet (n, i, v)   |
| \ne     | edit code snippet (n, i)       |
| \ntl    | edit local templates (n, i)    |
| \ntc    | edit custom templates (n, i)   |
| \ntp    | edit personal templates (n, i) |
| \ntr    | reread the templates (n, i)    |
| \ntw    | template setup wizard (n, i)   |
| \nts    | choose style (n, i)            |

# 运行命令
|  Run  |                                            |
| ----- | ------------------------------------------ |
| \rr  | update file, run script (n, i, v^1^)       |
| \ra  | set script cmd. line arguments (n, i)      |
| \rba | set Bash cmd. line arguments (n, i)        |
| \rc  | update file, check syntax (n, i)           |
| \rco | syntax check options (n, i)                |
| \rd  | start debugger^1^ (n, i)                   |
| \re  | make script executable/not exec.^1^ (n, i) |
| \rh  | hardcopy buffer (n, i, v)                  |
| \rs  | plug-in settings (n, i)                    |
| \rx  | set xterm size^1^^,^^2^ (n, i)             |
| \ro  | change output destination (n, i)           |

# 帮助命令
| Help |                                         |
| ---- | --------------------------------------- |
| \he  | English dictionary                      |
| \hb  | display the Bash manual                 |
| \hh  | help (Bash builtins)                    |
| \hm  | show manual (cmd. line utilities) (n,i) |
| \hp  | help (plug-in)                          |

# BASH 内置命令
| Bash  |                               |
| ----- | ----------------------------- |
| \\bps | parameter substitution (list) |
| \\bsp | special parameters (list)     |
| \\ben | environment (list)            |
| \\bbu | builtins (list)               |
| \\bse | set options (list)            |
| \\bso | shopts (list)                 |
