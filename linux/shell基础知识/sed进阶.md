---
title: sed进阶
description: sed进阶
published: true
date: 2021-08-23T03:36:17.368Z
tags: sed进阶
editor: markdown
dateCreated: 2020-07-18T08:17:07.387Z
---

# 多行命令
在使用sed编辑器的基础命令时，你可能注意到了一个局限。所有的sed编辑器命令都是针对单行数据执行操作的。在sed编辑器读取数据流时，它会基于换行符的位置将数据分成行。sed编辑器根据定义好的脚本命令一次处理一行数据，然后移到下一行重复这个过程。

有时需要对跨多行的数据执行特定操作。如果要查找或替换一个短语，就更是如此了。

举个例子，如果你正在数据中查找短语Linux System Administrators Group，它很有可能出现在两行中，每行各包含其中一部分短语。如果用普通的sed编辑器命令来处理文本，就不可能发现这种被分开的短语。

幸运的是，sed编辑器的设计人员已经考虑到了这种情况，并设计了对应的解决方案。sed编辑器包含了三个可用来处理多行文本的特殊命令。

- N：将数据流中的下一行加进来创建一个多行组（multiline group）来处理。

- D：删除多行组中的一行。

- P：打印多行组中的一行。

后面几节将会进一步讲解这些多行命令并向你演示如何在脚本中使用它们。

## next命令
在讲解多行next命令之前，首先需要看一下单行版本的next命令是如何工作的，然后就比较容易理解多行版本的next命令是如何操作的了。
**1. 单行的next命令**
小写的n命令会告诉sed编辑器移动到数据流中的下一文本行，而不用重新回到命令的最开始再执行一遍。记住，通常sed编辑器在移动到数据流中的下一文本行之前，会在当前行上执行完所有定义好的命令。单行next命令改变了这个流程。

这听起来可能有些复杂，没错，有时确实是。在这个例子中，你有个数据文件，共有5行内容，其中的两行是空的。目标是删除首行之后的空白行，而留下最后一行之前的空白行。如果写一个删掉空白行的sed脚本，你会删掉两个空白行。
```bash
$ cat data1.txt
This is the header line.

This is a data line.

This is the last line.
$
$ sed '/^$/d' data1.txt
This is the header line.
This is a data line.
This is the last line.
$

```

由于要删除的行是空行，没有任何能够标示这种行的文本可供查找。解决办法是用n命令。在这个例子中，脚本要查找含有单词header的那一行。找到之后，n命令会让sed编辑器移动到文本的下一行，也就是那个空行。
```bash
$ sed '/header/{n ; d}' data1.txt
This is the header line.
This is a data line.

This is the last line.
$
```
这时，sed编辑器会继续执行命令列表，该命令列表使用d命令来删除空白行。sed编辑器执行完命令脚本后，会从数据流中读取下一行文本，并从头开始执行命令脚本。因为sed编辑器再也找不到包含单词header的行了。所以也不会有其他行会被删掉。

**2. 合并文本行**

了解了单行版的next命令，现在来看看多行版的。单行next命令会将数据流中的下一文本行移动到sed编辑器的工作空间（称为模式空间）。多行版本的next命令（用大写N）会将下一文本行添加到模式空间中已有的文本后。

这样的作用是将数据流中的两个文本行合并到同一个模式空间中。文本行仍然用换行符分隔，但sed编辑器现在会将两行文本当成一行来处理。

下面的例子演示了N命令的工作方式。
```bash
$ cat data2.txt
This is the header line.
This is the first data line.
This is the second data line.
This is the last line.
$
$ sed '/first/{ N ; s/\n/ / }' data2.txt
This is the header line.
This is the first data line. This is the second data line.
This is the last line.
$
```
sed编辑器脚本查找含有单词first的那行文本。找到该行后，它会用N命令将下一行合并到那行，然后用替换命令s将换行符替换成空格。结果是，文本文件中的两行在sed编辑器的输出中成了一行。

如果要在数据文件中查找一个可能会分散在两行中的文本短语的话，这是个很实用的应用程序。这里有个例子。
```bash
$ cat data3.txt
On Tuesday, the Linux System
Administrator's group meeting will be held.
All System Administrators should attend.
Thank you for your attendance.
$
$ sed 'N ; s/System Administrator/Desktop User/' data3.txt
On Tuesday, the Linux System
Administrator's group meeting will be held.
All Desktop Users should attend.
Thank you for your attendance.
$
```
替换命令会在文本文件中查找特定的双词短语System Administrator。如果短语在一行中的话，事情很好处理，替换命令可以直接替换文本。但如果短语分散在两行中的话，替换命令就没法识别匹配的模式了。

这时N命令就可以派上用场了。
```bash
$ sed 'N ; s/System.Administrator/Desktop User/' data3.txt
On Tuesday, the Linux Desktop User's group meeting will be held.
All Desktop Users should attend.
Thank you for your attendance.
$
```
用N命令将发现第一个单词的那行和下一行合并后，即使短语内出现了换行，你仍然可以找到它。

注意，替换命令在System和Administrator之间用了通配符模式（.）来匹配空格和换行符这两种情况。但当它匹配了换行符时，它就从字符串中删掉了换行符，导致两行合并成一行。这可能不是你想要的。

要解决这个问题，可以在sed编辑器脚本中用两个替换命令：一个用来匹配短语出现在多行中的情况，一个用来匹配短语出现在单行中的情况。
```bash
$ sed 'N
> s/System\nAdministrator/Desktop\nUser/
> s/System Administrator/Desktop User/
> ' data3.txt
On Tuesday, the Linux Desktop
User's group meeting will be held.
All Desktop Users should attend.
Thank you for your attendance.
$
```
第一个替换命令专门查找两个单词间的换行符，并将它放在了替换字符串中。这样你就能在

第一个替换命令专门在两个检索词之间寻找换行符，并将其纳入替换字符串。这样就允许你在新文本的同样位置添加换行符了。

但这个脚本中仍有个小问题。这个脚本总是在执行sed编辑器命令前将下一行文本读入到模式空间。当它到了最后一行文本时，就没有下一行可读了，所以N命令会叫sed编辑器停止。如果要匹配的文本正好在数据流的最后一行上，命令就不会发现要匹配的数据
```bash
$ cat data4.txt
On Tuesday, the Linux System
Administrator's group meeting will be held.
All System Administrators should attend.
$
$ sed 'N
> s/System\nAdministrator/Desktop\nUser/
> s/System Administrator/Desktop User/
> ' data4.txt
On Tuesday, the Linux Desktop
User's group meeting will be held.
All System Administrators should attend.
$

```
由于System Administrator文本出现在了数据流中的最后一行，N命令会错过它，因为没有其他行可读入到模式空间跟这行合并。你可以轻松地解决这个问题——将单行命令放到N命令前面，并将多行命令放到N命令后面，像这样：
```bash
$ sed '
> s/System Administrator/Desktop User/
> N
> s/System\nAdministrator/Desktop\nUser/
> ' data4.txt
On Tuesday, the Linux Desktop
User's group meeting will be held.
All Desktop Users should attend.
$
```
现在，查找单行中短语的替换命令在数据流的最后一行也能正常工作，多行替换命令则会负责短语出现在数据流中间的情况。

## 多行删除命令
第19章介绍了单行删除命令（d）。sed编辑器用它来删除模式空间中的当前行。但和N命令一起使用时，使用单行删除命令就要小心了。
```bash
$ sed 'N ; /System\nAdministrator/d' data4.txt
All System Administrators should attend.
$
```
删除命令会在不同的行中查找单词System和Administrator，然后在模式空间中将两行都删掉。这未必是你想要的结果。

sed编辑器提供了多行删除命令D，它只删除模式空间中的第一行。该命令会删除到换行符（含换行符）为止的所有字符。
```bash
$ sed 'N ; /System\nAdministrator/D' data4.txt
Administrator's group meeting will be held.
All System Administrators should attend.
$
```
文本的第二行被N命令加到了模式空间，但仍然完好。如果需要删掉目标数据字符串所在行的前一文本行，它能派得上用场。

这里有个例子，它会删除数据流中出现在第一行前的空白行。
```bash
$ cat data5.txt

This is the header line.
This is a data line.

This is the last line.
$
$ sed '/^$/{N ; /header/D}' data5.txt
This is the header line.
This is a data line.

This is the last line.
$

```
## 多行打印命令
现在，你可能已经了解了单行和多行版本命令间的差异。多行打印命令（P）沿用了同样的方法。它只打印多行模式空间中的第一行。这包括模式空间中直到换行符为止的所有字符。当你用-n选项来阻止脚本输出时，它和显示文本的单行p命令的用法大同小异。
```bash
$ sed -n 'N ; /System\nAdministrator/P' data3.txt
On Tuesday, the Linux System
$
```
当多行匹配出现时，P命令只会打印模式空间中的第一行。多行P命令的强大之处在和N命令及D命令组合使用时才能显现出来。

D命令的独特之处在于强制sed编辑器返回到脚本的起始处，对同一模式空间中的内容重新执行这些命令（它不会从数据流中读取新的文本行）。在命令脚本中加入N命令，你就能单步扫过整个模式空间，将多行一起匹配。

接下来，使用P命令打印出第一行，然后用D命令删除第一行并绕回到脚本的起始处。一旦返回，N命令会读取下一行文本并重新开始这个过程。这个循环会一直继续下去，直到数据流结束。

# 保持空间
模式空间（pattern space）是一块活跃的缓冲区，在sed编辑器执行命令时它会保存待检查的文本。但它并不是sed编辑器保存文本的唯一空间。

sed编辑器有另一块称作保持空间（hold space）的缓冲区域。在处理模式空间中的某些行时，可以用保持空间来临时保存一些行。有5条命令可用来操作保持空间，见表21-1。
**表 21-1　sed编辑器的保持空间命令**
| 命令 |           描述            |
| --- | ------------------------- |
| `h` | 将模式空间复制到保持空间     |
| `H` | 将模式空间附加到保持空间     |
| `g` | 将保持空间复制到模式空间     |
| `G` | 将保持空间附加到模式空间     |
| `x` | 交换模式空间和保持空间的内容 |


这些命令用来将文本从模式空间复制到保持空间。这可以清空模式空间来加载其他要处理的字符串。

通常，在使用h或H命令将字符串移动到保持空间后，最终还要用g、G或x命令将保存的字符串移回模式空间（否则，你就不用在一开始考虑保存它们了）。

由于有两个缓冲区域，弄明白哪行文本在哪个缓冲区域有时会比较麻烦。这里有个简短的例子演示了如何用h和g命令来将数据在sed编辑器缓冲空间之间移动。

```bash
$ cat data2.txt
This is the header line.
This is the first data line.
This is the second data line.
This is the last line.
$
$ sed -n '/first/ {h ; p ; n ; p ; g ; p }' data2.txt
This is the first data line.
This is the second data line.
This is the first data line.
$
```
我们来一步一步看上面这个代码例子：

(1) sed脚本在地址中用正则表达式来过滤出含有单词first的行；

(2) 当含有单词first的行出现时，h命令将该行放到保持空间；

(3) p命令打印模式空间也就是第一个数据行的内容；

(4) n命令提取数据流中的下一行（This is the second data line），并将它放到模式空间；

(5) p命令打印模式空间的内容，现在是第二个数据行；

(6) g命令将保持空间的内容（This is the first data line）放回模式空间，替换当前文本；

(7) p命令打印模式空间的当前内容，现在变回第一个数据行了。

通过使用保持空间来回移动文本行，你可以强制输出中第一个数据行出现在第二个数据行后面。如果丢掉了第一个p命令，你可以以相反的顺序输出这两行。
```bash
$ sed -n '/first/ {h ; n ; p ; g ; p }' data2.txt
This is the second data line.
This is the first data line.
$

```
这是个有用的开端。你可以用这种方法来创建一个sed脚本将整个文件的文本行反转！但要那么做的话，你需要了解sed编辑器的排除特性，也就是下节的内容。


# 排除命令
第19章演示了sed编辑器如何将命令应用到数据流中的每一个文本行或是由单个地址或地址区间特别指定的多行。你也可以配置命令使其不要作用到数据流中的特定地址或地址区间。

感叹号命令（!）用来排除（negate）命令，也就是让原本会起作用的命令不起作用。下面的例子演示了这一特性。
```bash
$ sed -n '/header/!p' data2.txt
This is the first data line.
This is the second data line.
This is the last line.
$

```
普通p命令只打印data2文件中包含单词header的那行。加了感叹号之后，情况就相反了：除了包含单词header那一行外，文件中其他所有的行都被打印出来了。

感叹号在有些应用中用起来很方便。本章之前的21.1.1节演示了一种情况：sed编辑器无法处理数据流中最后一行文本，因为之后再没有其他行了。可以用感叹号来解决这个问题。
```bash
$ sed 'N;
> s/System\nAdministrator/Desktop\nUser/
> s/System Administrator/Desktop User/
> ' data4.txt
On Tuesday, the Linux Desktop
User's group meeting will be held.
All System Administrators should attend.
$
$ sed '$!N;
> s/System\nAdministrator/Desktop\nUser/
> s/System Administrator/Desktop User/
> ' data4.txt
On Tuesday, the Linux Desktop
User's group meeting will be held.
All Desktop Users should attend.
$
```
这个例子演示了如何配合使用感叹号与N命令以及与美元符特殊地址。美元符表示数据流中的最后一行文本，所以当sed编辑器到了最后一行时，它没有执行N命令，但它对所有其他行都执行了这个命令。

使用这种方法，你可以反转数据流中文本行的顺序。要实现这个效果（先显示最后一行，最后显示第一行），你得利用保持空间做一些特别的铺垫工作。

你得像这样使用模式空间：

(1) 在模式空间中放置一行；

(2) 将模式空间中的行放到保持空间中；

(3) 在模式空间中放入下一行；

(4) 将保持空间附加到模式空间后；

(5) 将模式空间中的所有内容都放到保持空间中；

(6)重复执行第(3)~(5)步，直到所有行都反序放到了保持空间中；

(7) 提取并打印行。

图21-1详细描述了这个过程。

![par-space.png](/shell/par-space.png)

**图 21-1　使用保持空间来反转文本文件中行的顺序**

在使用这种方法时，你不想在处理时打印行。这意味着要使用sed的-n命令行选项。下一步是决定如何将保持空间文本附加到模式空间文本后面。这可以用G命令完成。唯一的问题是你不想将保持空间附加到要处理的第一行文本后面。这可以用感叹号命令轻松解决.
下一步就是将新的模式空间（含有已反转的行）放到保持空间。这也非常简单，只要用h命令就行。

将模式空间中的整个数据流都反转了之后，你要做的就是打印结果。当到达数据流中的最后一行时，你就知道已经得到了模式空间的整个数据流。打印结果要用下面的命令：
```bash
$p

```
这些都是你创建可以反转行的sed编辑器脚本所需的操作步骤。现在可以运行一下试试.
```bash
$ cat data2.txt
This is the header line.
This is the first data line.
This is the second data line.
This is the last line.
$
$ sed -n '{1!G ; h ; $p }' data2.txt
This is the last line.
This is the second data line.
This is the first data line.
This is the header line.
$
```
sed编辑器脚本的执行和预期的一样。脚本输出反转了文本文件中原来的行。这展示了在sed脚本中使用保持空间的强大之处。它提供了一种在脚本输出中控制行顺序的简单办法。

> 说明　可能你想说，有个Linux命令已经有反转文本文件的功能了。tac命令会倒序显示一个文本文件。你也许已经注意到了，这个命令的名字很巧妙，它执行的正好是与cat命令相反的功能。
# 改变流
通常，sed编辑器会从脚本的顶部开始，一直执行到脚本的结尾（D命令是个例外，它会强制sed编辑器返回到脚本的顶部，而不读取新的行）。sed编辑器提供了一个方法来改变命令脚本的执行流程，其结果与结构化编程类似。
## 分支
在前面一节中，你了解了如何用感叹号命令来排除作用在某行上的命令。sed编辑器提供了一种方法，可以基于地址、地址模式或地址区间排除一整块命令。这允许你只对数据流中的特定行执行一组命令。

分支（branch）命令b的格式如下：
```bash
[address]b [label]
```
address参数决定了哪些行的数据会触发分支命令。label参数定义了要跳转到的位置。如果没有加label参数，跳转命令会跳转到脚本的结尾。
```bash
$ cat data2.txt
This is the header line.
This is the first data line.
This is the second data line.
This is the last line.
$
$ sed '{2,3b ; s/This is/Is this/ ; s/line./test?/}' data2.txt
Is this the header test?
This is the first data line.
This is the second data line.
Is this the last test?
$

```
分支命令在数据流中的第2行和第3行处跳过了两个替换命令。

要是不想直接跳到脚本的结尾，可以为分支命令定义一个要跳转到的标签。标签以冒号开始，最多可以是7个字符长度。
```bash
:label2
```

要指定标签，将它加到b命令后即可。使用标签允许你跳过地址匹配处的命令，但仍然执行脚本中的其他命令。

```bash
$ sed '{/first/b jump1 ; s/This is the/No jump on/
> :jump1
> s/This is the/Jump here on/}' data2.txt
No jump on header line
Jump here on first data line
No jump on second data line
No jump on last line
$
```
跳转命令指定如果文本行中出现了first，程序应该跳到标签为jump1的脚本行。如果分支命令的模式没有匹配，sed编辑器会继续执行脚本中的命令，包括分支标签后的命令（因此，所有的替换命令都会在不匹配分支模式的行上执行）。

如果某行匹配了分支模式， sed编辑器就会跳转到带有分支标签的那行。因此，只有最后一个替换命令会执行。

这个例子演示了跳转到sed脚本后面的标签上。也可以跳转到脚本中靠前面的标签上，这样就达到了循环的效果。
```bash
$ echo "This, is, a, test, to, remove, commas." | sed -n '{
> :start
> s/,//1p
> b start
> }'
This is, a, test, to, remove, commas.
This is a, test, to, remove, commas.
This is a test, to, remove, commas.
This is a test to, remove, commas.
This is a test to remove, commas.
This is a test to remove commas.
^C
$
```
脚本的每次迭代都会删除文本中的第一个逗号，并打印字符串。这个脚本有个问题：它永远不会结束。这就形成了一个无穷循环，不停地查找逗号，直到使用Ctrl+C组合键发送一个信号，手动停止这个脚本。

要防止这个问题，可以为分支命令指定一个地址模式来查找。如果没有模式，跳转就应该结束
```bash
$ echo "This, is, a, test, to, remove, commas." | sed -n '{
> :start
> s/,//1p
> /,/b start
> }'
This is, a, test, to, remove, commas.
This is a, test, to, remove, commas.
This is a test, to, remove, commas.
This is a test to, remove, commas.
This is a test to remove, commas.
This is a test to remove commas.
$
```
现在分支命令只会在行中有逗号的情况下跳转。在最后一个逗号被删除后，分支命令不会再执行，脚本也就能正常停止了。
## 测试
类似于分支命令，测试（test）命令（t）也可以用来改变sed编辑器脚本的执行流程。测试命令会根据替换命令的结果跳转到某个标签，而不是根据地址进行跳转。

如果替换命令成功匹配并替换了一个模式，测试命令就会跳转到指定的标签。如果替换命令未能匹配指定的模式，测试命令就不会跳转。

测试命令使用与分支命令相同的格式。
```bash
[address]t [label]
```
跟分支命令一样，在没有指定标签的情况下，如果测试成功，sed会跳转到脚本的结尾。

测试命令提供了对数据流中的文本执行基本的if-then语句的一个低成本办法。举个例子，如果已经做了一个替换，不需要再做另一个替换，那么测试命令能帮上忙。
```bash
$ sed '{
> s/first/matched/
> t
> s/This is the/No match on/
> }' data2.txt
No match on header line
This is the matched data line
No match on second data line
No match on last line
$
```
第一个替换命令会查找模式文本first。如果匹配了行中的模式，它就会替换文本，而且测试命令会跳过后面的替换命令。如果第一个替换命令未能匹配模式，第二个替换命令就会被执行。

有了测试命令，你就能结束之前用分支命令形成的无限循环。

```bash
$ echo "This, is, a, test, to, remove, commas. " | sed -n '{
> :start
> s/,//1p
> t start
> }'
This is, a, test, to, remove, commas.
This is a, test, to, remove, commas.
This is a test, to, remove, commas.
This is a test to, remove, commas.
This is a test to remove, commas.
This is a test to remove commas.
$
```

# 模式替代
你已经知道了如何在sed命令中使用模式来替代数据流中的文本。然而在使用通配符时，很难知道到底哪些文本会匹配模式。

举个例子，假如你想在行中匹配的单词两边上放上引号。如果你只是要匹配模式中的一个单词，那就非常简单。
```bash
$ echo "The cat sleeps in his hat." | sed 's/cat/"cat"/'
The "cat" sleeps in his hat.
$
```
但如果你在模式中用通配符（.）来匹配多个单词呢？
```bash
$ echo "The cat sleeps in his hat." | sed 's/.at/".at"/g'
The ".at" sleeps in his ".at".
$
```
模式字符串用点号通配符来匹配at前面的一个字母。遗憾的是，用于替代的字符串无法匹配已匹配单词中的通配符字符。


## &符号
sed编辑器提供了一个解决办法。&符号可以用来代表替换命令中的匹配的模式。不管模式匹配的是什么样的文本，你都可以在替代模式中使用&符号来使用这段文本。这样就可以操作模式所匹配到的任何单词了。
```bash
$ echo "The cat sleeps in his hat." | sed 's/.at/"&"/g'
The "cat" sleeps in his "hat".
$

```
当模式匹配了单词cat，"cat"就会出现在了替换后的单词里。当它匹配了单词hat，"hat"就出现在了替换后的单词中。
## 替代单独的单词
&符号会提取匹配替换命令中指定模式的整个字符串。有时你只想提取这个字符串的一部分。当然可以这么做，只是要稍微花点心思而已。

sed编辑器用圆括号来定义替换模式中的子模式。你可以在替代模式中使用特殊字符来引用每个子模式。替代字符由反斜线和数字组成。数字表明子模式的位置。sed编辑器会给第一个子模式分配字符\1，给第二个子模式分配字符\2，依此类推。

警告　当在替换命令中使用圆括号时，必须用转义字符将它们标示为分组字符而不是普通的圆括号。这跟转义其他特殊字符正好相反。

来看一个在sed编辑器脚本中使用这个特性的例子。
```bash
echo "The System Administrator manaual" | sed -r 's/(System) (Administrator)/\1 User/'这个替换命令用一对圆括号将单词System括起来，将其标示为一个子模式。然后它在替代模式中使用\1来提取第一个匹配的子模式。这没什么特别的，但在处理通配符模式时却特别有用。

如果需要用一个单词来替换一个短语，而这个单词刚好是该短语的子字符串，但那个子字符串碰巧使用了通配符，这时使用子模式会方便很多。
$
```
这个替换命令用一对圆括号将单词System括起来，将其标示为一个子模式。然后它在替代模式中使用\1来提取第一个匹配的子模式。这没什么特别的，但在处理通配符模式时却特别有用。

如果需要用一个单词来替换一个短语，而这个单词刚好是该短语的子字符串，但那个子字符串碰巧使用了通配符，这时使用子模式会方便很多。

```bash
 echo "That furry cat is pretty" | sed -r 's/furry (.at)/\1/'
```

在这种情况下，你不能用&符号，因为它会替换整个匹配的模式。子模式提供了答案，允许你选择将模式中的某部分作为替代模式。

当需要在两个或多个子模式间插入文本时，这个特性尤其有用。这里有个脚本，它使用子模式在大数字中插入逗号。
```bash
echo "1234567" | sed  -r '{:start ;s/(.*[0-9])([0-9]{3})/\1,\2/; t start }' 
1,234,567
$
```
这个脚本将匹配模式分成了两部分
```bash
.*[0-9]
[0-9]{3}
```

这个模式会查找两个子模式。第一个子模式是以数字结尾的任意长度的字符。第二个子模式是若干组三位数字（关于如何在正则表达式中使用花括号的内容可参考第20章）。如果这个模式在文本中找到了，替代文本会在两个子模式之间加一个逗号，每个子模式都会通过其位置来标示。这个脚本使用测试命令来遍历这个数字，直到放置好所有的逗号。

# 在脚本中使用sed
## 使用包装脚本
你可能已经注意到，实现sed编辑器脚本的过程很烦琐，尤其是脚本很长的话。可以将sed编辑器命令放到shell包装脚本（wrapper）中，不用每次使用时都重新键入整个脚本。包装脚本充当着sed编辑器脚本和命令行之间的中间人角色。

在shell脚本中，可以将普通的shell变量及参数和sed编辑器脚本一起使用。这里有个将命令行参数变量作为sed脚本输入的例子。
```bash
$ cat reverse.sh
#!/bin/bash
# Shell wrapper for sed editor script.
#               to reverse text file lines.
#
sed -n '{ 1!G ; h ; $p }' $1
#
$
```
名为reverse的shell脚本用sed编辑器脚本来反转数据流中的文本行。它使用shell参数$1从命令行中提取第一个参数，这正是需要进行反转的文件名。
```bash
$ ./reverse.sh data2.txt
This is the last line.
This is the second data line.
This is the first data line.
This is the header line.
$
```
现在你能在任何文件上轻松使用这个sed编辑器脚本，再不用每次都在命令行上重新输入了。
## 重定向sed的输出
默认情况下，sed编辑器会将脚本的结果输出到STDOUT上。你可以在shell脚本中使用各种标准方法对sed编辑器的输出进行重定向。

可以在脚本中用$()将sed编辑器命令的输出重定向到一个变量中，以备后用。下面的例子使用sed脚本来向数值计算结果添加逗号。
```bash
$ cat fact.sh
#!/bin/bash
# Add commas to number in factorial answer
#
factorial=1
counter=1
number=$1
#
while [ $counter -le $number ]
do
   factorial=$[ $factorial * $counter ]
   counter=$[ $counter + 1 ]
done
#
result=$(echo $factorial | sed '{
:start
s/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/
t start
}')
#
echo "The result is $result"
#
$
$ ./fact.sh 20
The result is 2,432,902,008,176,640,000
$
```
在使用普通的阶乘计算脚本后，脚本的结果会被作为sed编辑器脚本的输入，它会给结果加上逗号。然后echo语句使用这个值产生最终结果。

