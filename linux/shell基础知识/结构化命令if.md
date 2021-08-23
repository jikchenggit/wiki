---
title: bash 结构化命令(if,then,else,test)
description: bash 结构化命令(if,then,else,test)
published: true
date: 2021-08-23T03:29:53.889Z
tags: bash, if, then, else, test
editor: markdown
dateCreated: 2020-04-04T11:23:34.132Z
---

# 使用结构化命令
本章内容

- 使用if-then语句

- 嵌套if语句

- test命令

- 复合条件测试

- 使用双方括号和双括号

- case命令

## if-then 语句
最基本的结构化命令就是if-then语句。if-then语句有如下格式。
```bash
if command
then
    commands
fi
```
如果你在用其他编程语言的if-then语句，这种形式可能会让你有点困惑。在其他编程语言中，if语句之后的对象是一个等式，这个等式的求值结果为TRUE或FALSE。但bash shell的if语句并不是这么做的。

bash shell的if语句会运行if后面的那个命令。如果该命令的退出状态码（参见第11章）是0（该命令成功运行），位于then部分的命令就会被执行。如果该命令的退出状态码是其他值， then部分的命令就不会被执行，bash shell会继续执行脚本中的下一个命令。fi语句用来表示if-then语句到此结束。

这里有个简单的例子可解释这个概念。

```bash
$ cat test1.sh
#!/bin/bash
# testing the if statement
if pwd
then
    echo "It worked"
fi
$

```

shell执行了if行中的pwd命令。由于退出状态码是0，它就又执行了then部分的echo语句。
## if-then-else语句
在if-then语句中，不管命令是否成功执行，你都只有一种选择。如果命令返回一个非零退出状态码，bash shell会继续执行脚本中的下一条命令。在这种情况下，如果能够执行另一组命令就好了。这正是if-then-else语句的作用。

if-then-else语句在语句中提供了另外一组命令
```bash
if command
then
   commands
else
   commands
fi
```

## 嵌套if
有时你需要检查脚本代码中的多种条件。对此，可以使用嵌套的if-then语句。

要检查/etc/passwd文件中是否存在某个用户名以及该用户的目录是否尚在，可以使用嵌套的if-then语句。嵌套的if-then语句位于主if-then-else语句的else代码块中。

```bash
#!/bin/bash
# Testing nested ifs
#
testuser=NoSuchUser
#
if grep $testuser /etc/passwd
then
   echo "The user $testuser exists on this system."
else
   echo "The user $testuser does not exist on this system."
   if ls -d /home/$testuser/
   then
      echo "However, $testuser has a directory."
   fi
fi
```

**嵌套if简写**
```bash
if command1
then
   commands
elif command2
then
    more commands
fi
```

## test 命令
到目前为止，在if语句中看到的都是普通shell命令。你可能想问，if-then语句是否能测试命令退出状态码之外的条件。

答案是不能。但在bash shell中有个好用的工具可以帮你通过if-then语句测试其他条件。

test命令提供了在if-then语句中测试不同条件的途径。如果test命令中列出的条件成立，test命令就会退出并返回退出状态码0。这样if-then语句就与其他编程语言中的if-then语句以类似的方式工作了。如果条件不成立，test命令就会退出并返回非零的退出状态码，这使得if-then语句不会再被执行。

test命令的格式非常简单。
```bash
test condition
```
condition是test命令要测试的一系列参数和值。当用在if-then语句中时，test命令看起来是这样的。
```bash
if test condition
then
   commands
fi
```
如果不写test命令的condition部分，它会以非零的退出状态码退出，并执行else语句块。
```bash
$ cat test6.sh
#!/bin/bash
# Testing the test command
#
if test
then
   echo "No expression returns a True"
else
   echo "No expression returns a False"
fi
$
$ ./test6.sh
No expression returns a False
$
```
bash shell提供了另一种条件测试方法，无需在if-then语句中声明test命令。
```bash
if [ condition ]
then
   commands
fi
```
方括号定义了测试条件。注意，第一个方括号之后和第二个方括号之前必须加上一个空格，否则就会报错。
test命令可以判断三类条件：

- 数值比较

- 字符串比较

- 文件比较
### 数值比较
**test命令的数值比较功能**

|    比较     |           描述           |
| ----------- | ------------------------ |
| `n1 -eq n2` | 检查`n1`是否与`n2`相等     |
| `n1 -ge n2` | 检查`n1`是否大于或等于`n2` |
| `n1 -gt n2` | 检查`n1`是否大于`n2`      |
| `n1 -le n2` | 检查`n1`是否小于或等于`n2` |
| `n1 -lt n2` | 检查`n1`是否小于`n2`      |
| `n1 -ne n2` | 检查`n1`是否不等于`n2`     |

```bash
$ cat numeric_test.sh
#!/bin/bash
# Using numeric test evaluations
#
value1=10
value2=11
#
if [ $value1 -gt 5 ]
then
    echo "The test value $value1 is greater than 5"
fi
#
if [ $value1 -eq $value2 ]
then
    echo "The values are equal"
else
    echo "The values are different"
fi
#
$
```

但是涉及浮点值时，数值条件测试会有一个限制。
```bash
$ cat floating_point_test.sh
#!/bin/bash
# Using floating point numbers in test evaluations
#
value1=5.555
#
echo "The test value is $value1"
#
if [ $value1 -gt 5 ]
then
    echo "The test value $value1 is greater than 5"
fi
#
$ ./floating_point_test.sh
The test value is 5.555
./floating_point_test.sh: line 8:
[: 5.555: integer expression expected
$
```

此例，变量value1中存储的是浮点值。接着，脚本对这个值进行了测试。显然这里出错了。

> 记住，bash shell只能处理整数。如果你只是要通过echo语句来显示这个结果，那没问题。但是，在基于数字的函数中就不行了，例如我们的数值测试条件。最后一行就说明我们不能在test命令中使用浮点值。
{.is-danger}

### 字符串比较

**字符串比较测试**
|      比较      |           描述            |
| -------------- | ------------------------ |
| `str1 = str2`  | 检查`str1`是否和`str2`相同 |
| `str1 != str2` | 检查`str1`是否和`str2`不同 |
| `str1 < str2`  | 检查`str1`是否比`str2`小   |
| `str1 > str2`  | 检查`str1`是否比`str2`大   |
| `-n str1`      | 检查`str1`的长度是否非0    |
| `-z str1`      | 检查`str1`的长度是否为0    |


#### 字符串相等性
```bash
$ cat test7.sh
#!/bin/bash
# testing string equality
testuser=rich
#
if [ $USER = $testuser ]
then
   echo "Welcome $testuser"
fi
$
$ ./test7.sh
Welcome rich
$
```
字符串不等条件也可以判断两个字符串是否有相同的值。
```bash
$ cat test8.sh
#!/bin/bash
# testing string equality
testuser=baduser
#
if [ $USER != $testuser ]
then
   echo "This is not $testuser"
else
   echo "Welcome $testuser"
fi
$
$ ./test8.sh
This is not baduser
$
```
#### 字符串大小
要测试一个字符串是否比另一个字符串大就是麻烦的开始。当要开始使用测试条件的大于或小于功能时，就会出现两个经常困扰shell程序员的问题：

- 大于号和小于号必须转义，否则shell会把它们当作重定向符号，把字符串值当作文件名；

- 大于和小于顺序和sort命令所采用的不同。

在编写脚本时，第一条可能会导致一个不易察觉的严重问题。下面的例子展示了shell脚本编程初学者时常碰到的问题。
```bash
$ cat badtest.sh
#!/bin/bash
# mis-using string comparisons
#
val1=baseball
val2=hockey
#
if [ $val1 > $val2 ]
then
   echo "$val1 is greater than $val2"
else
   echo "$val1 is less than $val2"
fi
$
$ ./badtest.sh
baseball is greater than hockey
$ ls -l hockey
-rw-r--r--    1 rich     rich            0 Sep 30 19:08 hockey
$
```
这个脚本中只用了大于号，没有出现错误，但结果是错的。脚本把大于号解释成了输出重定向（参见第15章）。因此，它创建了一个名为hockey的文件。由于重定向的顺利完成，test命令返回了退出状态码0，if语句便以为所有命令都成功结束了。

要解决这个问题，就需要正确转义大于号。
```
$ cat test9.sh
#!/bin/bash
# mis-using string comparisons
#
val1=baseball
val2=hockey
#
if [ $val1 \> $val2 ]
then
  echo "$val1 is greater than $val2"
else
   echo "$val1 is less than $val2"
fi
$
$ ./test9.sh
baseball is less than hockey
$

```
第二个问题更细微，除非你经常处理大小写字母，否则几乎遇不到。sort命令处理大写字母的方法刚好跟test命令相反。让我们在脚本中测试一下这个特性。
```bash
$ cat test9b.sh
#!/bin/bash
# testing string sort order
val1=Testing
val2=testing
#
if [ $val1 \> $val2 ]
then
   echo "$val1 is greater than $val2"
else
   echo "$val1 is less than $val2"
fi
$
$ ./test9b.sh
Testing is less than testing
$
$ sort testfile
testing
Testing
$
```

在比较测试中，大写字母被认为是小于小写字母的。但sort命令恰好相反。当你将同样的字符串放进文件中并用sort命令排序时，小写字母会先出现。这是由各个命令使用的排序技术不同造成的。

比较测试中使用的是标准的ASCII顺序，根据每个字符的ASCII数值来决定排序结果。sort命令使用的是系统的本地化语言设置中定义的排序顺序。对于英语，本地化设置指定了在排序顺序中小写字母出现在大写字母前。

> 说明　test命令和测试表达式使用标准的数学比较符号来表示字符串比较，而用文本代码来表示数值比较。这个细微的特性被很多程序员理解反了。如果你对数值使用了数学运算符号，shell会将它们当成字符串值，可能无法得到正确的结果。

#### 字符串大小
-n和-z可以检查一个变量是否含有数据。
```bash
$ cat test10.sh
#!/bin/bash
# testing string length
val1=testing
val2=''
#
if [ -n $val1 ]
then
   echo "The string '$val1' is not empty"
else
   echo "The string '$val1' is empty"
fi
#
if [ -z $val2 ]
then
   echo "The string '$val2' is empty"
else
   echo "The string '$val2' is not empty"
fi
#
if [ -z $val3 ]
then
   echo "The string '$val3' is empty"
else
   echo "The string '$val3' is not empty"
fi
$
$ ./test10.sh
The string 'testing' is not empty
The string '' is empty
The string '' is empty
$
```

> 窍门　空的和未初始化的变量会对shell脚本测试造成灾难性的影响。如果不是很确定一个变量的内容，最好在将其用于数值或字符串比较之前先通过-n或-z来测试一下变量是否含有值。
{.is-info}
### 文件比较

**test命令的文件比较功能**

|       比较        |                  描述                  |
| ----------------- | -------------------------------------- |
| `-d file`         | 检查`file`是否存在并是一个目录            |
| `-e file`         | 检查`file`是否存在                      |
| `-f file`         | 检查`file`是否存在并是一个文件            |
| `-r file`         | 检查`file`是否存在并可读                 |
| `-s file`         | 检查`file`是否存在并非空                 |
| `-w file`         | 检查`file`是否存在并可写                 |
| `-x file`         | 检查`file`是否存在并可执行               |
| `-O file`         | 检查`file`是否存在并属当前用户所有        |
| `-G file`         | 检查`file`是否存在并且默认组与当前用户相同 |
| `file1 -nt file2` | 检查`file1`是否比`file2`新              |
| `file1 -ot file2` | 检查`file1`是否比`file2`旧              |

#### 检查目录
-d测试会检查指定的目录是否存在于系统中。如果你打算将文件写入目录或是准备切换到某个目录中，先进行测试总是件好事情。
```bash
$ cat test11.sh
#!/bin/bash
# Look before you leap
#
jump_directory=/home/arthur
#
if [ -d $jump_directory ]
then
   echo "The $jump_directory directory exists"
   cd $jump_directory
   ls
else
   echo "The $jump_directory directory does not exist"
fi
#
$
$ ./test11.sh
The /home/arthur directory does not exist
$
```
示例代码中使用了-d测试条件来检查jump_directory变量中的目录是否存在：若存在，就使用cd命令切换到该目录并列出目录中的内容；若不存在，脚本就输出一条警告信息，然后退出。
#### 检查对象是否存在



-e比较允许你的脚本代码在使用文件或目录前先检查它们是否存在。
```bash
$ cat test12.sh
#!/bin/bash
# Check if either a directory or file exists
#
location=$HOME
file_name="sentinel"
#
if [ -e $location ]
then  #Directory does exist
   echo "OK on the $location directory."
   echo "Now checking on the file, $file_name."
   #
   if [ -e $location/$file_name ]
   then #File does exist
       echo "OK on the filename"
       echo "Updating Current Date..."
       date >> $location/$file_name
   #
   else #File does not exist
       echo "File does not exist"
       echo "Nothing to update"
   fi
#
else   #Directory does not exist
   echo "The $location directory does not exist."
   echo "Nothing to update"
fi
#
$
$ ./test12.sh
OK on the /home/Christine directory.
Now checking on the file, sentinel.
File does not exist
Nothing to update
$
$ touch sentinel
$
$ ./test12.sh
OK on the /home/Christine directory.
Now checking on the file, sentinel.
OK on the filename
Updating Current Date...
$
```
第一次检查用-e比较来判断用户是否有$HOME目录。如果有，接下来的-e比较会检查sentinel文件是否存在于$HOME目录中。如果不存在，shell脚本就会提示该文件不存在，不需要进行更新。

为确保更新操作能够正常进行，我们创建了sentinel文件，然后重新运行这个shell脚本。这一次在进行条件测试时，$HOME和sentinel文件都存在，因此当前日期和时间就被追加到了文件中。

3. 检查文件

-e比较可用于文件和目录。要确定指定对象为文件，必须用-f比较。
```bash
$ cat test13.sh
#!/bin/bash
# Check if either a directory or file exists
#
item_name=$HOME
echo
echo "The item being checked: $item_name"
echo
#
if [ -e $item_name ]
then  #Item does exist
   echo "The item, $item_name, does exist."
   echo "But is it a file?"
   echo
   #
   if [ -f $item_name ]
   then #Item is a file
       echo "Yes, $item_name is a file."
   #
   else #Item is not a file
       echo "No, $item_name is not a file."
   fi
#
else   #Item does not exist
   echo "The item, $item_name, does not exist."
   echo "Nothing to update"
fi
#
$ ./test13.sh

The item being checked: /home/Christine

The item, /home/Christine, does exist.
But is it a file?

No, /home/Christine is not a file.
$
```
这一小段脚本进行了大量的检查！它首先使用-e比较测试$HOME是否存在。如果存在，继续用-f来测试它是不是一个文件。如果它不是文件（当然不会是了），就会显示一条消息，表明这不是一个文件。

我们对变量item_name作了一个小小的修改，将目录$HOME替换成文件$HOME/sentinel，结果就不一样了。
```bash
$ nano test13.sh
$
$ cat test13.sh
#!/bin/bash
# Check if either a directory or file exists
#
item_name=$HOME/sentinel
[...]
$
$ ./test13.sh

The item being checked: /home/Christine/sentinel

The item, /home/Christine/sentinel, does exist.
But is it a file?

Yes, /home/Christine/sentinel is a file.
$
```
这里只列出了脚本test13.sh的部分代码，因为只改变了脚本变量item_name的值。当运行这个脚本时，对$HOME/sentinel进行的-f测试所返回的退出状态码为0，then语句得以执行，然后输出消息：`Yes, /home/Christine/sentinel is a file`。

#### 检查是否可读

在尝试从文件中读取数据之前，最好先测试一下文件是否可读。可以使用-r比较测试。

```bash
$ cat test14.sh
#!/bin/bash
# testing if you can read a file
pwfile=/etc/shadow
#
# first, test if the file exists, and is a file
if [ -f $pwfile ]
then
   # now test if you can read it
   if [ -r $pwfile ]
   then
      tail $pwfile
   else
      echo "Sorry, I am unable to read the $pwfile file"
   fi
else
   echo "Sorry, the file $file does not exist"
fi
$
$ ./test14.sh
Sorry, I am unable to read the /etc/shadow file
$
```
/etc/shadow文件含有系统用户加密后的密码，所以它对系统上的普通用户来说是不可读的。-r比较确定该文件不允许进行读取，因此测试失败，bash shell执行了if-then语句的else部分。

####  检查空文件

应该用-s比较来检查文件是否为空，尤其是在不想删除非空文件的时候。要留心的是，当-s比较成功时，说明文件中有数据。
```bash
$ cat test15.sh
#!/bin/bash
# Testing if a file is empty
#
file_name=$HOME/sentinel
#
if [ -f $file_name ]
then
   if [ -s $file_name ]
   then
      echo "The $file_name file exists and has data in it."
      echo "Will not remove this file."
#
   else
      echo "The $file_name file exists, but is empty."
      echo "Deleting empty file..."
      rm $file_name
   fi
else
   echo "File, $file_name, does not exist."
fi
#
$ ls -l $HOME/sentinel
-rw-rw-r--. 1 Christine Christine 29 Jun 25 05:32 /home/Christine/sentinel
$
$ ./test15.sh
The /home/Christine/sentinel file exists and has data in it.
Will not remove this file.
$
```
-f比较测试首先测试文件是否存在。如果存在，由-s比较来判断该文件是否为空。空文件会被删除。可以从ls -l的输出中看出sentinel并不是空文件，因此脚本并不会删除它。

#### 检查是否可写

-w比较会判断你对文件是否有可写权限。脚本test16.sh只是脚本test13.sh的修改版。现在不单检查item_name是否存在、是否为文件，还会检查该文件是否有写入权限。
```bash
$ cat test16.sh
#!/bin/bash
# Check if a file is writable.
#
item_name=$HOME/sentinel
echo
echo "The item being checked: $item_name"
echo
[...]
       echo "Yes, $item_name is a file."
       echo "But is it writable?"
       echo
       #
       if [ -w $item_name ]
       then #Item is writable
            echo "Writing current time to $item_name"
            date +%H%M >> $item_name
       #
       else #Item is not writable
            echo "Unable to write to $item_name"
       fi
   #
   else #Item is not a file
       echo "No, $item_name is not a file."
   fi
[...]
$
$ ls -l sentinel
-rw-rw-r--. 1 Christine Christine 0 Jun 27 05:38 sentinel
$
$ ./test16.sh

The item being checked: /home/Christine/sentinel

The item, /home/Christine/sentinel, does exist.
But is it a file?

Yes, /home/Christine/sentinel is a file.
But is it writable?

Writing current time to /home/Christine/sentinel
$
$ cat sentinel
0543
$
```
变量item_name被设置成$HOME/sentinel，该文件允许用户进行写入（有关文件权限的更多信息，请参见第7章）。因此当脚本运行时，-w测试表达式会返回非零退出状态，然后执行then代码块，将时间戳写入文件sentinel中。

如果使用chmod关闭文件sentinel的用户 写入权限，-w测试表达式会返回非零的退出状态码，时间戳不会被写入文件。
```bash
$ chmod u-w sentinel
$
$ ls -l sentinel
-r--rw-r--. 1 Christine Christine 5 Jun 27 05:43 sentinel
$
$ ./test16.sh

The item being checked: /home/Christine/sentinel

The item, /home/Christine/sentinel, does exist.
But is it a file?

Yes, /home/Christine/sentinel is a file.
But is it writable?

Unable to write to /home/Christine/sentinel
$
```
chmod命令可用来为读者再次回授写入权限。这会使得写入测试表达式返回退出状态码0，并允许一次针对文件的写入尝试。

#### 检查文件是否可以执行

-x比较是判断特定文件是否有执行权限的一个简单方法。虽然可能大多数命令用不到它，但如果你要在shell脚本中运行大量脚本，它就能发挥作用。
```bash
$ cat test17.sh
#!/bin/bash
# testing file execution
#
if [ -x test16.sh ]
then
   echo "You can run the script: "
   ./test16.sh
else
   echo "Sorry, you are unable to execute the script"
fi
$
$ ./test17.sh
You can run the script:
[...]
$
$ chmod u-x test16.sh
$
$ ./test17.sh
Sorry, you are unable to execute the script
$
```
这段示例shell脚本用-x比较来测试是否有权限执行test16.sh脚本。如果有权限，它会运行这个脚本。在首次成功运行test16.sh脚本后，更改文件的权限。这次，-x比较失败了，因为你已经没有test16.sh脚本的执行权限了。

#### 检查所属关系

-O比较可以测试出你是否是文件的属主。
```bash
$ cat test18.sh
#!/bin/bash
# check file ownership
#
if [ -O /etc/passwd ]
then
   echo "You are the owner of the /etc/passwd file"
else
   echo "Sorry, you are not the owner of the /etc/passwd file"
fi
$
$ ./test18.sh
Sorry, you are not the owner of the /etc/passwd file
$
```
这段脚本用-O比较来测试运行该脚本的用户是否是/etc/passwd文件的属主。这个脚本是运行在普通用户账户下的，所以测试失败了。

#### 检查默认属组关系

-G比较会检查文件的默认组，如果它匹配了用户的默认组，则测试成功。由于-G比较只会检查默认组而非用户所属的所有组，这会叫人有点困惑。这里有个例子。
```bash
$ cat test19.sh
#!/bin/bash
# check file group test
#
if [ -G $HOME/testing ]
then
   echo "You are in the same group as the file"
else
   echo "The file is not owned by your group"
fi
$
$ ls -l $HOME/testing
-rw-rw-r-- 1 rich rich 58 2014-07-30 15:51 /home/rich/testing
$
$ ./test19.sh
You are in the same group as the file
$
$ chgrp sharing $HOME/testing
$
$ ./test19
The file is not owned by your group
$
```
第一次运行脚本时，$HOME/testing文件属于rich组，所以通过了-G比较。接下来，组被改成了sharing组，用户也是其中的一员。但是，-G比较失败了，因为它只比较默认组，不会去比较其他的组。

#### 检查文件日期

最后一组方法用来对两个文件的创建日期进行比较。这在编写软件安装脚本时非常有用。有时候，你不会愿意安装一个比系统上已有文件还要旧的文件。

-nt比较会判定一个文件是否比另一个文件新。如果文件较新，那意味着它的文件创建日期更近。-ot比较会判定一个文件是否比另一个文件旧。如果文件较旧，意味着它的创建日期更早。

```bash
$ cat test20.sh
#!/bin/bash
# testing file dates
#
if [ test19.sh -nt test18.sh ]
then
   echo "The test19 file is newer than test18"
else
   echo "The test18 file is newer than test19"
fi
if [ test17.sh -ot test19.sh ]
then
  echo "The test17 file is older than the test19 file"
fi
$
$ ./test20.sh
The test19 file is newer than test18
The test17 file is older than the test19 file
$
$ ls -l test17.sh test18.sh test19.sh
-rwxrw-r-- 1 rich rich 167 2014-07-30 16:31 test17.sh
-rwxrw-r-- 1 rich rich 185 2014-07-30 17:46 test18.sh
-rwxrw-r-- 1 rich rich 167 2014-07-30 17:50 test19.sh
$
```
用于比较文件路径是相对你运行该脚本的目录而言的。如果你要检查的文件已经移走，就会出现问题。另一个问题是，这些比较都不会先检查文件是否存在。试试这个测试。

```bash
$ cat test21.sh
#!/bin/bash
# testing file dates
#
if [ badfile1 -nt badfile2 ]
then
   echo "The badfile1 file is newer than badfile2"
else
   echo "The badfile2 file is newer than badfile1"
fi
$
$ ./test21.sh
The badfile2 file is newer than badfile1
$
```
这个小例子演示了如果文件不存在，-nt比较会返回一个错误的结果。在你尝试使用-nt或-ot比较文件之前，必须先确认文件是存在的。
## 复合条件测试
if-then语句允许你使用布尔逻辑来组合测试。有两种布尔运算符可用：

- [ condition1 ] && [ condition2 ]

- [ condition1 ] || [ condition2 ]

第一种布尔运算使用AND布尔运算符来组合两个条件。要让then部分的命令执行，两个条件都必须满足。

窍门　布尔逻辑是一种能够将可能的返回值简化为TRUE或FALSE的方法。
第二种布尔运算使用OR布尔运算符来组合两个条件。如果任意条件为TRUE，then部分的命令就会执行。

下例展示了AND布尔运算符的使用。
```bash
$ cat test22.sh
#!/bin/bash
# testing compound comparisons
#
if [ -d $HOME ] && [ -w $HOME/testing ]
then
   echo "The file exists and you can write to it"
else
   echo "I cannot write to the file"
fi
$
$ ./test22.sh
I cannot write to the file
$
$ touch $HOME/testing
$
$ ./test22.sh
The file exists and you can write to it
$
```
使用AND布尔运算符时，两个比较都必须满足。第一个比较会检查用户的$HOME目录是否存在。第二个比较会检查在用户的$HOME目录是否有个叫testing的文件，以及用户是否有该文件的写入权限。如果两个比较中的一个失败了，if语句就会失败，shell就会执行else部分的命令。如果两个比较都通过了，则if语句通过，shell会执行then部分的命令。

## if-then 高级特性
bash shell提供了两项可在if-then语句中使用的高级特性：

- 用于数学表达式的双括号

- 用于高级字符串处理功能的双方括号

后面几节将会详细描述每一种特性。
### 使用双括号
双括号命令允许你在比较过程中使用高级数学表达式。test命令只能在比较中使用简单的算术操作。双括号命令提供了更多的数学符号，这些符号对于用过其他编程语言的程序员而言并不陌生。双括号命令的格式如下：
```bash
(( expression ))
```
expression可以是任意的数学赋值或比较表达式。除了test命令使用的标准数学运算符，表12-4列出了双括号命令中会用到的其他运算符。
　**表12-4 双括号命令符号**
 
|  符号   |   描述   |
| ------- | ------- |
| `val++` | 后增     |
| `val--` | 后减     |
| `++val` | 先增     |
| `--val` | 先减     |
| `!`     | 逻辑求反 |
| `~`     | 位求反   |
| `**`    | 幂运算   |
| `<<`    | 左位移   |
| `>>`    | 右位移   |
| `&`     | 位布尔和 |
| `|`     | 位布尔或 |
| `&&`    | 逻辑和   |
| `||`    | 逻辑或   |

可以在if语句中用双括号命令，也可以在脚本中的普通命令里使用来赋值。
```bash
$ cat test23.sh
#!/bin/bash
# using double parenthesis
#
val1=10
#
if (( $val1 ** 2 > 90 ))
then
   (( val2 = $val1 ** 2 ))
   echo "The square of $val1 is $val2"
fi
$
$ ./test23.sh
The square of 10 is 100
$
```
> 注意，不需要将双括号中表达式里的大于号转义。这是双括号命令提供的另一个高级特性。
{.is-warning}

### 使用双方括号
双方括号命令提供了针对字符串比较的高级特性。双方括号命令的格式如下：
```bash
[[ expression ]]
```

双方括号里的expression使用了test命令中采用的标准字符串比较。但它提供了test命令未提供的另一个特性——模式匹配（pattern matching）。
> 	说明　双方括号在bash shell中工作良好。不过要小心，不是所有的shell都支持双方括号。
{.is-warning}

在模式匹配中，可以定义一个正则表达式（将在第20章中详细讨论）来匹配字符串值。
```bash
$ cat test24.sh
#!/bin/bash
# using pattern matching
#
if [[ $USER == r* ]]
then
   echo "Hello $USER"
else
   echo "Sorry, I do not know you"
fi
$
$ ./test24.sh
Hello rich
$
```

在上面的脚本中，我们使用了双等号（==）。双等号将右边的字符串（r*）视为一个模式，并应用模式匹配规则。双方括号命令$USER环境变量进行匹配，看它是否以字母r开头。如果是的话，比较通过，shell会执行then部分的命令。

# case命令 
你会经常发现自己在尝试计算一个变量的值，在一组可能的值中寻找特定值。在这种情形下，你不得不写出很长的if-then-else语句，就像下面这样。

elif语句继续if-then检查，为比较变量寻找特定的值。
```bash
$ cat test25.sh
#!/bin/bash
# looking for a possible value
#
if [ $USER = "rich" ]
then
   echo "Welcome $USER"
   echo "Please enjoy your visit"
elif [ $USER = "barbara" ]
then
   echo "Welcome $USER"
   echo "Please enjoy your visit"
elif [ $USER = "testing" ]
then
   echo "Special testing account"
elif [ $USER = "jessica" ]
then
   echo "Do not forget to logout when you're done"
else
   echo "Sorry, you are not allowed here"
fi
$
$ ./test25.sh
Welcome rich
Please enjoy your visit
$
```
有了case命令，就不需要再写出所有的elif语句来不停地检查同一个变量的值了。case命令会采用列表格式来检查单个变量的多个值。
```bash
case variable in
pattern1 | pattern2) commands1;;
pattern3) commands2;;
*) default commands;;
esac
```
case命令会将指定的变量与不同模式进行比较。如果变量和模式是匹配的，那么shell会执行为该模式指定的命令。可以通过竖线操作符在一行中分隔出多个模式模式。星号会捕获所有与已知模式不匹配的值。这里有个将if-then-else程序转换成用case命令的例子。
```bash
$ cat test26.sh
#!/bin/bash
# using the case command
#
case $USER in
rich | barbara)
   echo "Welcome, $USER"
   echo "Please enjoy your visit";;
testing)
  echo "Special testing account";;
jessica)
   echo "Do not forget to log off when you're done";;
*)
   echo "Sorry, you are not allowed here";;
esac
$
$ ./test26.sh
Welcome, rich
Please enjoy your visit
$
```
