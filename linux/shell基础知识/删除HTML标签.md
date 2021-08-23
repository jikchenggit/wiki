---
title: 删除HTML标签
description: 删除HTML标签
published: true
date: 2021-08-23T03:37:08.310Z
tags: 删除html标签
editor: markdown
dateCreated: 2020-07-18T15:11:21.951Z
---

现如今，从网站下载文本并将其保存或用作应用程序的数据并不罕见。但当你从网站下载文本时，有时其中也包含了用于数据格式化的HTML标签。如果你只是查看数据，这会是个问题。

标准的HTML Web页面包含一些不同类型的HTML标签，标明了正确显示页面信息所需要的格式化功能。这里有个HTML文件的例子。
```bash
$ cat data11.txt
<html>
<head>
<title>This is the page title</title>
</head>
<body>
<p>
This is the <b>first</b> line in the Web page.
This should provide some <i>useful</i>
information to use in our sed script.
</body>
</html>
$
```
HTML标签由小于号和大于号来识别。大多数HTML标签都是成对出现的：一个起始标签（比如`<b>`用来加粗），以及另一个结束标签（比如`</b>`用来结束加粗）。

但如果不够小心的话，删除HTML标签可能会带来问题。乍一看，你可能认为删除HTML标签的办法就是查找以小于号（<）开头、大于号（>）结尾且其中有数据的文本字符串：
```bash
s/<.*>//g
```
很遗憾，这个命令会出现一些意料之外的结果。
```bash
$ sed 's/<.*>//g' data11.txt






This is the  line in the Web page.
This should provide some
information to use in our sed script.


$
```
注意，标题文本以及加粗和倾斜的文本都不见了。sed编辑器将这个脚本忠实地理解为小于号和大于号之间的任何文本，且包括其他的小于号和大于号。每次文本出现在HTML标签中（比如`<b>`first`</b>`），这个sed脚本都会删掉整个文本。

这个问题的解决办法是让sed编辑器忽略掉任何嵌入到原始标签中的大于号。要这么做的话，你可以创建一个字符组来排除大于号。脚本改为：
```bash
$ sed 's/<[^>]*>//g ; /^$/d' data11.txt
This is the page title
This is the first line in the Web page.
This should provide some useful
information to use in our sed script.
$
```
现在紧凑多了，只有你想要看的数据。

