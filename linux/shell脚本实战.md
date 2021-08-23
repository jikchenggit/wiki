---
title: shell脚本实战
description: shell脚本实战
published: true
date: 2021-08-23T03:50:28.914Z
tags: shell脚本实战
editor: markdown
dateCreated: 2020-10-05T16:12:05.270Z
---

# 常用代码库

## 验证命令是否存在
```bash
#!/bin/bash
  # inpath -- 验证指定程序是否有效，或者能否在PATH目录列表中找到。

  in_path()
  {
    # 尝试在环境变量PATH中找到给定的命令。如果找到，返回0；
    # 如果没有找到，则返回1。注意，该函数会临时修改IFS（内部字段分隔符），
    # 不过在函数执行完毕时会将其恢复原状。

    cmd=$1        ourpath=$2        result=1
    oldIFS=$IFS    IFS=":"

    for directory in $ourpath
    do
      if [ -x $directory/$cmd ] ; then
        result=0      # 如果执行到此处，那么表明我们已经找到了该命令。
      fi
    done

    IFS=$oldIFS
    return $result
  }

  checkForCmdInPath()
  {
    var=$1

    if [ "$var" != "" ] ; then
❶     if [ "${var:0:1}" = "/" ] ; then
❷       if [ !  -x $var ] ; then
          return 1
        fi
❸     elif ! in_path $var "$PATH" ; then
        return 2
      fi
    fi
    
    
 if [ $# -ne 1 ] ; then
  echo "Usage: $0 command" >&2
  exit 1
fi

checkForCmdInPath "$1"
case $? in
  0 ) echo "$1 found in PATH"                   ;;
  1 ) echo "$1 not found or not executable"     ;;
  2 ) echo "$1 not found in PATH"               ;;
esac

exit 0
    ```