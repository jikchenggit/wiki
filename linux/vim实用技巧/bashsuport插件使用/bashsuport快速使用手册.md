---
title: bashsuport快速使用手册
description: bashsuport快速使用手册
published: true
date: 2021-08-23T03:34:23.283Z
tags: bashsuport快速使用手册
editor: markdown
dateCreated: 2020-07-08T15:58:24.134Z
---

# 写在前面
1. 所有映射((\)+charater(s) combination) 都是特定于文件类型的:只对`sh` 文件有效.
2. 输入速度很重要,识别时间为3秒.
# 如何为新脚本生成一个自动头文件
请参见以下图片:使用本插件就会自动生成以下头文件.

![](https://www.tecmint.com/wp-content/uploads/2017/02/Script-Header-Options.png)

首先要设置个人信息(姓名,作者,组织,公司) .快速键入`\ntw` 命令启动模板安装向导.
选择第一项个性化文件,然后按`Enter`.

![](https://www.tecmint.com/wp-content/uploads/2017/02/Set-Personalization-in-Scripts.png)
然后再次按下`Enter`,选择1 个性化文件的位置,
![](https://www.tecmint.com/wp-content/uploads/2017/02/Set-Personalization-File-Location.png)
这个向导将会把模板文件` .vim/bash-support/rc/personal.templates` 复制到`.vim/templates/personal.templates` 路径下.然后打开,就可以进行详细模板输入了.
输入对应正确的值.

![](https://www.tecmint.com/wp-content/uploads/2017/02/Add-Info-in-Script-Header.png)

设置文成之后,保存和关闭推出文件,打开另一个脚本,文件头应该就有个人的详细信息.
![](https://www.tecmint.com/wp-content/uploads/2017/02/Auto-Adds-Header-to-Script.png)
# 如何在shell 脚本中插入注释
1. 如果要加框注释,请在`normal` 模式下键入`\cfr`.

![](https://www.tecmint.com/wp-content/uploads/2017/02/Add-Comments-to-Scripts.png)

# 如何在shell 脚本中插入语句
下面是插入语句的快捷键
```
\sc – case in … esac (n, I)
\sei – elif then (n, I)
\sf – for in do done (n, i, v)
\sfo – for ((…)) do done (n, i, v)
\si – if then fi (n, i, v)
\sie – if then else fi (n, i, v)
\ss – select in do done (n, i, v)
\su – until do done (n, i, v)
\sw – while do done (n, i, v)
\sfu – function (n, i, v)
\se – echo -e “…” (n, i, v)
\sp – printf “…” (n, i, v)
\sa – array element, ${.[.]} (n, i, v) and many more array features.
```
# 如何在vim 编辑器调试bash
以下是在vim中运行脚本.
```
\rr – update file, run script (n, I)
\ra – set script cmd line arguments (n, I)
\rc – update file, check syntax (n, I)
\rco – syntax check options (n, I)
\rd – start debugger (n, I)
\re – make script executable/not exec.(*) (in)
```
![](https://www.tecmint.com/wp-content/uploads/2017/02/make-script-executable.png)
# 如何使用代码片段
预定义的代码片段是包含特定目的的代码文件,如果要添加代码片段,请输入`\nr` 和`\nw` 读取和编写代码片段.
代码片段存储在以下位置:
```
.vim/bash-support/codesnippets/
```
![](https://www.tecmint.com/wp-content/uploads/2017/02/list-of-code-snippets.png)
使用代码片段时,键入`\nr` 然后使用自动补全命令,补全其名称.
![](https://www.tecmint.com/wp-content/uploads/2017/02/Add-Code-Snippet-to-Script.png)
# 创建自定义的预定义代码片段
可以在`.vim/bash-support/codesnippets/` 编写自己的代码片段.
可以使用`\nw` 创建新的代码片段.然后使用`\nr` 读取.
# 查看当前语句或命令的帮助
\hh – for built-in help
\hm – for a command help
![](https://www.tecmint.com/wp-content/uploads/2017/02/View-Built-in-Command-Help.png)


快速使用指南到此位置.如果需要更多命令:请下载文件:[bash-hotkeys.pdf](/shell/bash-hotkeys.pdf)