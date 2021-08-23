---
title: csv文件导入数据库
description: csv文件导入数据库
published: true
date: 2021-08-23T03:31:45.284Z
tags: csv, 数据库, 导入
editor: markdown
dateCreated: 2020-06-02T15:17:22.112Z
---

# csv文件导入数据库	
文件重定向常见于脚本需要读入文件和输出文件时。这个样例脚本两件事都做了。它读取.csv格式的数据文件，输出SQL INSERT语句来将数据插入数据库（参见第25章）。

shell脚本使用命令行参数指定待读取的.csv文件。.csv格式用于从电子表格中导出数据，所以你可以把数据库数据放入电子表格中，把电子表格保存成.csv格式，读取文件，然后创建INSERT语句将数据插入MySQL数据库。
```bash
$cat test23
#!/bin/bash
# read file and create INSERT statements for MySQL

outfile='members.sql'
IFS=','
while read lname fname address city state zip
do
   cat >> $outfile << EOF
   INSERT INTO members (lname,fname,address,city,state,zip) VALUES
('$lname', '$fname', '$address', '$city', '$state', '$zip');
EOF
done < ${1}
$
```
这个脚本很短小，这都要感谢有了文件重定向！脚本中出现了三处重定向操作。while循环使用read语句（参见第14章）从数据文件中读取文本。注意在done语句中出现的重定向符号：
```bash
done < ${1}
```
当运行程序test23时，$1代表第一个命令行参数。它指明了待读取数据的文件。read语句会使用IFS字符解析读入的文本，我们在这里将IFS指定为逗号。

脚本中另外两处重定向操作出现在同一条语句中：
```bash
cat >> $outfile << EOF
```
这条语句中包含一个输出追加重定向（双大于号）和一个输入追加重定向（双小于号）。输出重定向将cat命令的输出追加到由$outfile变量指定的文件中。cat命令的输入不再取自标准输入，而是被重定向到脚本中存储的数据。EOF符号标记了追加到文件中的数据的起止。
```bash
INSERT INTO members (lname,fname,address,city,state,zip) VALUES
('$lname', '$fname', '$address', '$city', '$state', '$zip');
```
上面的文本生成了一个标准的SQL INSERT语句。注意，其中的数据会由变量来替换，变量中内容则是由read语句存入的。

所以基本上while循环一次读取一行数据，将这些值放入INSERT语句模板中，然后将结果输出到输出文件中。

在这个例子中，使用以下输入数据文件。
```bash
$ cat members.csv
Blum,Richard,123 Main St.,Chicago,IL,60601
Blum,Barbara,123 Main St.,Chicago,IL,60601
Bresnahan,Christine,456 Oak Ave.,Columbus,OH,43201
Bresnahan,Timothy,456 Oak Ave.,Columbus,OH,43201
$
```
运行脚本时，显示器上不会出现任何输出：
```bash
$ ./test23 < members.csv
$
```
但是在members.sql输出文件中，你会看到如下输出内容。
```bash
$ cat members.sql
   INSERT INTO members (lname,fname,address,city,state,zip) VALUES ('Blum',
 'Richard', '123 Main St.', 'Chicago', 'IL', '60601');
   INSERT INTO members (lname,fname,address,city,state,zip) VALUES ('Blum',
 'Barbara', '123 Main St.', 'Chicago', 'IL', '60601');
   INSERT INTO members (lname,fname,address,city,state,zip) VALUES ('Bresnahan',
 'Christine', '456 Oak Ave.', 'Columbus', 'OH', '43201');
   INSERT INTO members (lname,fname,address,city,state,zip) VALUES ('Bresnahan',
 'Timothy', '456 Oak Ave.', 'Columbus', 'OH', '43201');
$
```