---
title: 结构化循环命令
description: 循环命令
published: true
date: 2021-08-23T03:30:12.517Z
tags: 循环遍历, for, until, while
editor: markdown
dateCreated: 2020-04-09T07:31:35.989Z
---

# 结构化循环命令
重复执行一系列命令在编程中很常见。通常你需要重复一组命令直至达到某个特定条件，比如处理某个目录下的所有文件、系统上的所有用户或是某个文本文件中的所有行。

bash shell提供了for命令，允许你创建一个遍历一系列值的循环。每次迭代都使用其中一个值来执行已定义好的一组命令。下面是bash shell中for命令的基本格式。
```bash
for var in list
do
    commands
done

```

在list参数中，你需要提供迭代中要用到的一系列值。可以通过几种不同的方法指定列表中的值。

在每次迭代中，变量var会包含列表中的当前值。第一次迭代会使用列表中的第一个值，第二次迭代使用第二个值，以此类推，直到列表中的所有值都过一遍。

在do和done语句之间输入的命令可以是一条或多条标准的bash shell命令。在这些命令中，$var变量包含着这次迭代对应的当前列表项中的值。

> 说明　只要你愿意，也可以将do语句和for语句放在同一行，但必须用分号将其同列表中的值分开：`for var in list; do`。

## 读取列表中的值
```bash
#!/bin/bash
# testing the for variable after the looping

for test in Alabama Alaska Arizona Arkansas California Colorado
do
   echo "The next state is $test"
done
echo "The last state we visited was $test"
test=Connecticut
echo "Wait, now we're visiting $test"
```
## 读取列表复杂值
```bash
$ cat badtest1
#!/bin/bash
# another example of how not to use the for command

for test in I don't know if this'll work
do
    echo "word:$test"
done
$ ./badtest1
word:I
word:dont know if thisll
word:work
$
```
真麻烦。shell看到了列表值中的单引号并尝试使用它们来定义一个单独的数据值，这真是把事情搞得一团糟。
有两种办法可解决这个问题：

- 使用转义字符（反斜线）来将单引号转义；

- 使用双引号来定义用到单引号的值。

for命令用空格来划分列表中的每个值。如果在单独的数据值中有空格，就必须用双引号将这些值圈起来
```bash
$ cat test3
#!/bin/bash
# an example of how to properly define values


for test in Nevada "New Hampshire" "New Mexico" "New York"
do
    echo "Now going to $test"
done
$ ./test3
Now going to Nevada
Now going to New Hampshire
Now going to New Mexico
Now going to New York
$
```
## 从变量读取列表
```bash
#!/bin/bash
# using a variable to hold the list

list="Alabama Alaska Arizona Arkansas Colorado"
list=$list" Connecticut"

for state in $list
do
    echo "Have you ever visited $state?"
done
```
## 从命令读取列表
```bash
$ cat test5
#!/bin/bash
# reading values from a file

file="states"

for state in $(cat $file)
do
    echo "Visit beautiful $state"
done
$ cat states
Alabama
Alaska
Arizona
Arkansas
Colorado
Connecticut
Delaware
Florida
Georgia
$ ./test5
Visit beautiful Alabama
Visit beautiful Alaska
Visit beautiful Arizona
Visit beautiful Arkansas
Visit beautiful Colorado
Visit beautiful Connecticut
Visit beautiful Delaware
Visit beautiful Florida
Visit beautiful Georgia
$
```

这个例子在命令替换中使用了cat命令来输出文件states的内容。你会注意到states文件中每一行有一个州，而不是通过空格分隔的。for命令仍然以每次一行的方式遍历了cat命令的输出，假定每个州都是在单独的一行上。但这并没有解决数据中有空格的问题。如果你列出了一个名字中有空格的州，for命令仍然会将每个单词当作单独的值。这是有原因的，下一节我们将会了解。

说明　test5的代码范例将文件名赋给变量，文件名中没有加入路径。这要求文件和脚本位于同一个目录中。如果不是的话，你需要使用全路径名（不管是绝对路径还是相对路径）来引用文件位置。
## 更改for 循环读取的字段分隔符

造成这个问题的原因是特殊的环境变量IFS，叫作内部字段分隔符（internal field separator）。IFS环境变量定义了bash shell用作字段分隔符的一系列字符。默认情况下，bash shell会将下列字符当作字段分隔符：

- 空格

- 制表符

- 换行符

如果bash shell在数据中看到了这些字符中的任意一个，它就会假定这表明了列表中一个新数据字段的开始。在处理可能含有空格的数据（比如文件名）时，这会非常麻烦，就像你在上一个脚本示例中看到的。

要解决这个问题，可以在shell脚本中临时更改IFS环境变量的值来限制被bash shell当作字段分隔符的字符。例如，如果你想修改IFS的值，使其只能识别换行符，那就必须这么做：
```bash
IFS=$'\n'
```

将这个语句加入到脚本中，告诉bash shell在数据值中忽略空格和制表符。对前一个脚本使用这种方法，将获得如下输出。
```bash
$ cat test5b
#!/bin/bash
# reading values from a file

file="states"

IFS=$'\n'
for state in $(cat $file)
do
    echo "Visit beautiful $state"
done
$ ./test5b
Visit beautiful Alabama
Visit beautiful Alaska
Visit beautiful Arizona
Visit beautiful Arkansas
Visit beautiful Colorado
Visit beautiful Connecticut
Visit beautiful Delaware
Visit beautiful Florida
Visit beautiful Georgia
Visit beautiful New York
Visit beautiful New Hampshire
Visit beautiful North Carolina
$
```
> 警告
> 
> 在处理代码量较大的脚本时，可能在一个地方需要修改IFS的值，然后忽略这次修改，在脚本的其他地方继续沿用IFS的默认值。一个可参考的安全实践是在改变IFS之前保存原来的IFS值，之后再恢复它。
{.is-warning}
```bash
IFS.OLD=$IFS
IFS=$'\n'
<在代码中使用新的IFS值>
IFS=$IFS.OLD
```
这就保证了在脚本的后续操作中使用的是IFS的默认值。

还有其他一些IFS环境变量的绝妙用法。假定你要遍历一个文件中用冒号分隔的值（比如在/etc/passwd文件中）。你要做的就是将IFS的值设为冒号。
```bash
IFS=:
```
如果要指定多个IFS字符，只要将它们在赋值行串起来就行.
```bash
IFS=$'\n':;"
```
这个赋值会将换行符、冒号、分号和双引号作为字段分隔符。如何使用IFS字符解析数据没有任何限制。

##  用通配符读取目录
最后，可以用for命令来自动遍历目录中的文件。进行此操作时，必须在文件名或路径名中使用通配符。它会强制shell使用文件扩展匹配。文件扩展匹配是生成匹配指定通配符的文件名或路径名的过程。

如果不知道所有的文件名，这个特性在处理目录中的文件时就非常好用。
```bash
$ cat test6
#!/bin/bash
# iterate through all the files in a directory

for file in /home/rich/test/*
do

    if [ -d "$file" ]
    then
       echo "$file is a directory"
    elif [ -f "$file" ]
    then
       echo "$file is a file"
    fi
done
$ ./test6
/home/rich/test/dir1 is a directory
/home/rich/test/myprog.c is a file
/home/rich/test/myprog is a file
/home/rich/test/myscript is a file
/home/rich/test/newdir is a directory
/home/rich/test/newfile is a file
/home/rich/test/newfile2 is a file
/home/rich/test/testdir is a directory
/home/rich/test/testing is a file
/home/rich/test/testprog is a file
/home/rich/test/testprog.c is a file
$
```
for命令会遍历`/home/rich/test/*`输出的结果。该代码用test命令测试了每个条目（使用方括号方法），以查看它是目录（通过-d参数）还是文件（通过-f参数）（参见第12章）。

注意，我们在这个例子的if语句中做了一些不同的处理：
```bash
if [ -d "$file" ]
```

在Linux中，目录名和文件名中包含空格当然是合法的。要适应这种情况，应该将$file变量用双引号圈起来。如果不这么做，遇到含有空格的目录名或文件名时就会有错误产生。
```bash
./test6: line 6: [: too many arguments
./test6: line 9: [: too many arguments
```
在test命令中，bash shell会将额外的单词当作参数，进而造成错误。

也可以在for命令中列出多个目录通配符，将目录查找和列表合并进同一个for语句。
```bash
$ cat test7
#!/bin/bash
# iterating through multiple directories

for file in /home/rich/.b* /home/rich/badtest
do
    if [ -d "$file" ]
    then
       echo "$file is a directory"
    elif [ -f "$file" ]
    then
       echo "$file is a file"
    else
      echo "$file doesn't exist"
    fi
done
$ ./test7
/home/rich/.backup.timestamp is a file
/home/rich/.bash_history is a file
/home/rich/.bash_logout is a file
/home/rich/.bash_profile is a file
/home/rich/.bashrc is a file
/home/rich/badtest doesn't exist
$
```
for语句首先使用了文件扩展匹配来遍历通配符生成的文件列表，然后它会遍历列表中的下一个文件。可以将任意多的通配符放进列表中。

> 警告　注意，你可以在数据列表中放入任何东西。即使文件或目录不存在，for语句也会尝试处理列表中的内容。在处理文件或目录时，这可能会是个问题。你无法知道你正在尝试遍历的目录是否存在：在处理之前测试一下文件或目录总是好的。
{.is-warning}
# C 语言风格的for命令
如果你从事过C语言编程，可能会对bash shell中for命令的工作方式有点惊奇。在C语言中，for循环通常定义一个变量，然后这个变量会在每次迭代时自动改变。通常程序员会将这个变量用作计数器，并在每次迭代中让计数器增一或减一。bash的for命令也提供了这个功能。本节将会告诉你如何在bash shell脚本中使用C语言风格的for命令。

C语言的for命令有一个用来指明变量的特定方法，一个必须保持成立才能继续迭代的条件，以及另一个在每个迭代中改变变量的方法。当指定的条件不成立时，for循环就会停止。条件等式通过标准的数学符号定义。比如，考虑下面的C语言代码：
```bash
for (i = 0; i < 10; i++)
{
    printf("The next number is %d\n", i);
}
```
这段代码产生了一个简单的迭代循环，其中变量i作为计数器。第一部分将一个默认值赋给该变量。中间的部分定义了循环重复的条件。当定义的条件不成立时，for循环就停止迭代。最后一部分定义了迭代的过程。在每次迭代之后，最后一部分中定义的表达式会被执行。在本例中，i变量会在每次迭代后增一。

bash shell也支持一种for循环，它看起来跟C语言风格的for循环类似，但有一些细微的不同，其中包括一些让shell脚本程序员困惑的东西。以下是bash中C语言风格的for循环的基本格式。
```bash
for (( variable assignment ; condition ; iteration process ))
```

C语言风格的for循环的格式会让bash shell脚本程序员摸不着头脑，因为它使用了C语言风格的变量引用方式而不是shell风格的变量引用方式。C语言风格的for命令看起来如下。
```bash
for (( a = 1; a < 10; a++ ))
```
注意，有些部分并没有遵循bash shell标准的for命令
- 变量赋值可以有空格；

- 条件中的变量不以美元符开头；

- 迭代过程的算式未用expr命令格式。

shell开发人员创建了这种格式以更贴切地模仿C语言风格的for命令。这虽然对C语言程序员来说很好，但也会把专家级的shell程序员弄得一头雾水。在脚本中使用C语言风格的for循环时要小心。

以下例子是在bash shell程序中使用C语言风格的for命令。
```bash
 cat test8
#!/bin/bash
# testing the C-style for loop

for (( i=1; i <= 10; i++ ))
do
    echo "The next number is $i"
done
$ ./test8
The next number is 1
The next number is 2
The next number is 3
The next number is 4
The next number is 5
The next number is 6
The next number is 7
The next number is 8
The next number is 9
The next number is 10
$
```
for循环通过定义好的变量（本例中是变量i）来迭代执行这些命令。在每次迭代中，$i变量包含了for循环中赋予的值。在每次迭代后，循环的迭代过程会作用在变量上，在本例中，变量增一。
## 使用多个变量
C语言风格的for命令也允许为迭代使用多个变量。循环会单独处理每个变量，你可以为每个变量定义不同的迭代过程。尽管可以使用多个变量，但你只能在for循环中定义一种条件。
```bash
$ cat test9
#!/bin/bash
# multiple variables

for (( a=1, b=10; a <= 10; a++, b-- ))
do
    echo "$a - $b"
done
$ ./test9
1 - 10
2 - 9
3 - 8
4 - 7
5 - 6
6 - 5
7 - 4
8 - 3
9 - 2
10 - 1
$
```
变量a和b分别用不同的值来初始化并且定义了不同的迭代过程。循环的每次迭代在增加变量a的同时减小了变量b。
# while 命令
while命令某种意义上是if-then语句和for循环的混杂体。while命令允许定义一个要测试的命令，然后循环执行一组命令，只要定义的测试命令返回的是退出状态码0。它会在每次迭代的一开始测试test命令。在test命令返回非零退出状态码时，while命令会停止执行那组命令。
## while的基本格式
```bash
while test command
do
  other commands
done
```
while命令中定义的test command和if-then语句（参见第12章）中的格式一模一样。可以使用任何普通的bash shell命令，或者用test命令进行条件测试，比如测试变量值。

while命令的关键在于所指定的test command的退出状态码必须随着循环中运行的命令而改变。如果退出状态码不发生变化，while循环就将一直不停地进行下去。

最常见的test command的用法是用方括号来检查循环命令中用到的shell变量的值。
```bash
$ cat test10
#!/bin/bash
# while command test

var1=10
while [ $var1 -gt 0 ]
do
    echo $var1
    var1=$[ $var1 - 1 ]
done
$ ./test10
10
9
8
7
6
5
4
3
2
1
$
```
while命令定义了每次迭代时检查的测试条件：
```bash
while [ $var1 -gt 0 ]
```
while循环会在测试条件不再成立时停止.
## 使用多个条件
while命令允许你在while语句行定义多个测试命令。只有最后一个测试命令的退出状态码会被用来决定什么时候结束循环。如果你不够小心，可能会导致一些有意思的结果。下面的例子将说明这一点。
```bash
$ cat test11
#!/bin/bash
# testing a multicommand while loop

var1=10

while echo $var1
       [ $var1 -ge 0 ]
do
    echo "This is inside the loop"
    var1=$[ $var1 - 1 ]
done
$ ./test11
10
This is inside the loop
9
This is inside the loop
8
This is inside the loop
7
This is inside the loop
6
This is inside the loop
5
This is inside the loop
4
This is inside the loop
3
This is inside the loop
2
This is inside the loop
1
This is inside the loop
0
This is inside the loop
-1
$
```
请仔细观察本例中做了什么。while语句中定义了两个测试命令。
```bash
while echo $var1
      [ $var1 -ge 0 ]
```
第一个测试简单地显示了var1变量的当前值。第二个测试用方括号来判断var1变量的值。在循环内部，echo语句会显示一条简单的消息，说明循环被执行了。注意当你运行本例时输出是如何结束的。
```bash
This is inside the loop
-1
$
```
while循环会在var1变量等于0时执行echo语句，然后将var1变量的值减一。接下来再次执行测试命令，用于下一次迭代。echo测试命令被执行并显示了var变量的值（现在小于0了）。直到shell执行test测试命令，whle循环才会停止。

这说明在含有多个命令的while语句中，在每次迭代中所有的测试命令都会被执行，包括测试命令失败的最后一次迭代。要留心这种用法。另一处要留意的是该如何指定多个测试命令。注意，每个测试命令都出现在单独的一行上。

# untill 命令
until命令和while命令工作的方式完全相反。until命令要求你指定一个通常返回非零退出状态码的测试命令。只有测试命令的退出状态码不为0，bash shell才会执行循环中列出的命令。一旦测试命令返回了退出状态码0，循环就结束了。

和你想的一样，until命令的格式如下。
```bash
until test commands
do
    other commands
done
```

和while命令类似，你可以在until命令语句中放入多个测试命令。只有最后一个命令的退出状态码决定了bash shell是否执行已定义的other commands。

下面是使用until命令的一个例子。
```bash
$ cat test12
#!/bin/bash
# using the until command

var1=100

until [ $var1 -eq 0 ]
do
    echo $var1
    var1=$[ $var1 - 25 ]
done
$ ./test12
100
75
50
25
$
```
本例中会测试var1变量来决定until循环何时停止。只要该变量的值等于0，until命令就会停止循环。同while命令一样，在until命令中使用多个测试命令时要注意。
```bash
$ cat test13
#!/bin/bash
# using the until command

var1=100

until echo $var1
       [ $var1 -eq 0 ]
do
    echo Inside the loop: $var1
    var1=$[ $var1 - 25 ]
done
$ ./test13
100
Inside the loop: 100
75
Inside the loop: 75
50
Inside the loop: 50
25
Inside the loop: 25
0
$
```
# 嵌套循环
循环语句可以在循环内使用任意类型的命令，包括其他循环命令。这种循环叫作嵌套循环（nested loop）。注意，在使用嵌套循环时，你是在迭代中使用迭代，与命令运行的次数是乘积关系。不注意这点的话，有可能会在脚本中造成问题。

这里有个在for循环中嵌套for循环的简单例子。
```bash
$ cat test14
#!/bin/bash
# nesting for loops

for (( a = 1; a <= 3; a++ ))
do
    echo "Starting loop $a:"
    for (( b = 1; b <= 3; b++ ))
    do
       echo "   Inside loop: $b"
    done
done
$ ./test14
Starting loop 1:
    Inside loop: 1
    Inside loop: 2
    Inside loop: 3
Starting loop 2:
    Inside loop: 1
    Inside loop: 2
    Inside loop: 3
Starting loop 3:
    Inside loop: 1
    Inside loop: 2
    Inside loop: 3
$
```

这个被嵌套的循环（也称为内部循环，inner loop）会在外部循环的每次迭代中遍历一次它所有的值。注意，两个循环的do和done命令没有任何差别。bash shell知道当第一个done命令执行时是指内部循环而非外部循环。

在混用循环命令时也一样，比如在while循环内部放置一个for循环。
```bash
$ cat test15
#!/bin/bash
# placing a for loop inside a while loop

var1=5

while [ $var1 -ge 0 ]
do
    echo "Outer loop: $var1"
    for (( var2 = 1; $var2 < 3; var2++ ))
    do
       var3=$[ $var1 * $var2 ]
       echo "  Inner loop: $var1 * $var2 = $var3"
    done
    var1=$[ $var1 - 1 ]
done
$ ./test15
Outer loop: 5
   Inner loop: 5 * 1 = 5
   Inner loop: 5 * 2 = 10
Outer loop: 4
   Inner loop: 4 * 1 = 4
   Inner loop: 4 * 2 = 8
Outer loop: 3
   Inner loop: 3 * 1 = 3
   Inner loop: 3 * 2 = 6
Outer loop: 2
   Inner loop: 2 * 1 = 2
   Inner loop: 2 * 2 = 4
Outer loop: 1
   Inner loop: 1 * 1 = 1
   Inner loop: 1 * 2 = 2
Outer loop: 0
   Inner loop: 0 * 1 = 0
   Inner loop: 0 * 2 = 0
$
```

同样，shell能够区分开内部for循环和外部while循环各自的do和done命令。

如果真的想挑战脑力，可以混用until和while循环。
```bash

$ cat test16
#!/bin/bash
# using until and while loops

var1=3

until [ $var1 -eq 0 ]
do
    echo "Outer loop: $var1"
    var2=1
    while [ $var2 -lt 5 ]
    do
       var3=$(echo "scale=4; $var1 / $var2" | bc)
       echo "   Inner loop: $var1 / $var2 = $var3"
       var2=$[ $var2 + 1 ]
    done
    var1=$[ $var1 - 1 ]
done
$ ./test16
Outer loop: 3
    Inner loop: 3 / 1 = 3.0000
    Inner loop: 3 / 2 = 1.5000
    Inner loop: 3 / 3 = 1.0000
    Inner loop: 3 / 4 = .7500
Outer loop: 2
    Inner loop: 2 / 1 = 2.0000
    Inner loop: 2 / 2 = 1.0000
    Inner loop: 2 / 3 = .6666
    Inner loop: 2 / 4 = .5000
Outer loop: 1
    Inner loop: 1 / 1 = 1.0000
    Inner loop: 1 / 2 = .5000
    Inner loop: 1 / 3 = .3333
    Inner loop: 1 / 4 = .2500
$
```

# 循环处理文件数据
通常必须遍历存储在文件中的数据。这要求结合已经讲过的两种技术：

- 使用嵌套循环

- 修改IFS环境变量

通过修改IFS环境变量，就能强制for命令将文件中的每行都当成单独的一个条目来处理，即便数据中有空格也是如此。一旦从文件中提取出了单独的行，可能需要再次利用循环来提取行中的数据。

典型的例子是处理/etc/passwd文件中的数据。这要求你逐行遍历/etc/passwd文件，并将IFS变量的值改成冒号，这样就能分隔开每行中的各个数据段了。
```bash
#!/bin/bash
# changing the IFS value

IFS.OLD=$IFS
IFS=$'\n'
for entry in $(cat /etc/passwd)
do
    echo "Values in $entry -"
    IFS=:
    for value in $entry
    do
       echo "   $value"
    done
done
$
```
这个脚本使用了两个不同的IFS值来解析数据。第一个IFS值解析出/etc/passwd文件中的单独的行。内部for循环接着将IFS的值修改为冒号，允许你从/etc/passwd的行中解析出单独的值。

在运行这个脚本时，你会得到如下输出。
```
Values in rich:x:501:501:Rich Blum:/home/rich:/bin/bash -
   rich
   x
    501
    501
    Rich Blum
    /home/rich
    /bin/bash
 Values in katie:x:502:502:Katie Blum:/home/katie:/bin/bash -
    katie
    x
    506
    509
    Katie Blum
    /home/katie
    /bin/bash
```

内部循环会解析出/etc/passwd每行中的各个值。这种方法在处理外部导入电子表格所采用的逗号分隔的数据时也很方便。
# 控制循环
你可能会想，一旦启动了循环，就必须苦等到循环完成所有的迭代。并不是这样的。有两个命令能帮我们控制循环内部的情况：

- break命令

- continue命令

每个命令在如何控制循环的执行方面有不同的用法。下面几节将介绍如何使用这些命令来控制循环。
## break 命令
break命令是退出循环的一个简单方法。可以用break命令来退出任意类型的循环，包括while和until循环。

有几种情况可以使用break命令，本节将介绍这些方法。
**1. 跳出单个循环**

在shell执行break命令时，它会尝试跳出当前正在执行的循环
```bash
$ cat test17
#!/bin/bash
# breaking out of a for loop

for var1 in 1 2 3 4 5 6 7 8 9 10
do
   if [ $var1 -eq 5 ]
   then
      break
   fi
   echo "Iteration number: $var1"
done
echo "The for loop is completed"
$ ./test17
Iteration number: 1
Iteration number: 2
Iteration number: 3
Iteration number: 4
The for loop is completed
$
```
for循环通常都会遍历列表中指定的所有值。但当满足if-then的条件时，shell会执行break命令，停止for循环。

这种方法同样适用于while和until循环。
```bash
$ cat test18
#!/bin/bash
# breaking out of a while loop

var1=1

while [ $var1 -lt 10 ]
do
   if [ $var1 -eq 5 ]
   then
      break
   fi
   echo "Iteration: $var1"
   var1=$[ $var1 + 1 ]
done
echo "The while loop is completed"
$ ./test18
Iteration: 1
Iteration: 2
Iteration: 3
Iteration: 4
The while loop is completed
$
```
while循环会在if-then的条件满足时执行break命令，终止.
**2. 跳出内部循环**
在处理多个循环时，break命令会自动终止你所在的最内层的循环。
```bash
$ cat test19
#!/bin/bash
# breaking out of an inner loop

for (( a = 1; a < 4; a++ ))
do
   echo "Outer loop: $a"
   for (( b = 1; b < 100; b++ ))
   do
      if [ $b -eq 5 ]
      then
         break
      fi
      echo "   Inner loop: $b"
   done
done
$ ./test19
Outer loop: 1
   Inner loop: 1
   Inner loop: 2
   Inner loop: 3
   Inner loop: 4
Outer loop: 2
   Inner loop: 1
   Inner loop: 2
   Inner loop: 3
   Inner loop: 4
Outer loop: 3
   Inner loop: 1
   Inner loop: 2
   Inner loop: 3
   Inner loop: 4
$
```

内部循环里的for语句指明当变量b等于100时停止迭代。但内部循环的if-then语句指明当变量b的值等于5时执行break命令。注意，即使内部循环通过break命令终止了，外部循环依然继续执行。

**3. 跳出外部循环**
有时你在内部循环，但需要停止外部循环。break命令接受单个命令行参数值：
```bash
break n
```
其中n指定了要跳出的循环层级。默认情况下，n为1，表明跳出的是当前的循环。如果你将n设为2，break命令就会停止下一级的外部循环。
```bash
$ cat test20
#!/bin/bash
# breaking out of an outer loop

for (( a = 1; a < 4; a++ ))
do
   echo "Outer loop: $a"
   for (( b = 1; b < 100; b++ ))
   do
      if [ $b -gt 4 ]
      then
         break 2
      fi
      echo "   Inner loop: $b"
   done
done
$ ./test20
Outer loop: 1
   Inner loop: 1
   Inner loop: 2
   Inner loop: 3
   Inner loop: 4
$
```
注意，当shell执行了break命令后，外部循环就停止了。
## continue 命令
continue命令可以提前中止某次循环中的命令，但并不会完全终止整个循环。可以在循环内部设置shell不执行命令的条件。这里有个在for循环中使用continue命令的简单例子。
```bash
$ cat test21
#!/bin/bash
# using the continue command

for (( var1 = 1; var1 < 15; var1++ ))
do
   if [ $var1 -gt 5 ] && [ $var1 -lt 10 ]
   then
      continue
   fi
   echo "Iteration number: $var1"
done
$ ./test21
Iteration number: 1
Iteration number: 2
Iteration number: 3
Iteration number: 4
Iteration number: 5
Iteration number: 10
Iteration number: 11
Iteration number: 12
Iteration number: 13
Iteration number: 14
$
```

当if-then语句的条件被满足时（值大于5且小于10），shell会执行continue命令，跳过此次循环中剩余的命令，但整个循环还会继续。当if-then的条件不再被满足时，一切又回到正轨。

也可以在while和until循环中使用continue命令，但要特别小心。记住，当shell执行continue命令时，它会跳过剩余的命令。如果你在其中某个条件里对测试条件变量进行增值，问题就会出现。
```bash
$ cat badtest3
#!/bin/bash
# improperly using the continue command in a while loop

var1=0

while echo "while iteration: $var1"
      [ $var1 -lt 15 ]
do
   if [ $var1 -gt 5 ] && [ $var1 -lt 10 ]
   then
      continue
   fi
   echo "   Inside iteration number: $var1"
   var1=$[ $var1 + 1 ]
done
$ ./badtest3 | more
while iteration: 0
   Inside iteration number: 0
while iteration: 1
   Inside iteration number: 1
while iteration: 2
   Inside iteration number: 2
while iteration: 3
   Inside iteration number: 3
while iteration: 4
   Inside iteration number: 4
while iteration: 5
   Inside iteration number: 5
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
while iteration: 6
$
```
你得确保将脚本的输出重定向到了more命令，这样才能停止输出。在if-then的条件成立之前，所有一切看起来都很正常，然后shell执行了continue命令。当shell执行continue命令时，它跳过了while循环中余下的命令。不幸的是，被跳过的部分正是$var1计数变量增值的地方，而这个变量又被用于while测试命令中。这意味着这个变量的值不会再变化了，从前面连续的输出显示中你也可以看出来。

和break命令一样，continue命令也允许通过命令行参数指定要继续执行哪一级循环：
```bash
continue n
```
其中n定义了要继续的循环层级。下面是继续外部for循环的一个例子。
```bash
$ cat test22
#!/bin/bash
# continuing an outer loop

for (( a = 1; a <= 5; a++ ))
do
   echo "Iteration $a:"
   for (( b = 1; b < 3; b++ ))
   do
      if [ $a -gt 2 ] && [ $a -lt 4 ]
      then
         continue 2
      fi
      var3=$[ $a * $b ]
      echo "   The result of $a * $b is $var3"
   done
done
$ ./test22
Iteration 1:
   The result of 1 * 1 is 1
   The result of 1 * 2 is 2
Iteration 2:
   The result of 2 * 1 is 2
   The result of 2 * 2 is 4
Iteration 3:
Iteration 4:
   The result of 4 * 1 is 4
   The result of 4 * 2 is 8
Iteration 5:
   The result of 5 * 1 is 5
   The result of 5 * 2 is 10
$

```