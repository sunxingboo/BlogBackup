---
layout: post
title: sort与uniq命令的使用
date: 2020-03-19
tags: Linux
---

> **sort命令**将文件进行排序，并将排序结果标准输出。sort命令既可以从特定的文件，也可以从stdin中获取输入。
>
> **uniq命令**用于报告或忽略文件中的重复行，一般与sort命令结合使用。

<!--more-->



**sort命令的选项参数**

```bash
-b	--ignore-leading-blanks		默认情况下整行都会进行排序，添加这个选项sort会忽略行开头的空格，从第一个非空白字符开始排序
-f	--ignore-case							排序时不区分字母大小写
-n	--numeric-sort						按数值大小排序
-r	--reverse									逆序排序
-k	--key=filed1[,filed2]			对指定的列进行排序，而不是整个文本行
-m	--merge										将每个输入参数当作以排好序的文件名。将多个文件合并为一个排好序的文件而不执行额外的排序操作
-o	--output=file							将排序结果输出到文件而不是标准输出
-t	--filed-separator=char		定义字段分隔符，默认分隔符为空格或制表符
-u 	--unique									去除重复行并排序
```

`-k`选项的用法相对比较复杂

创建一段示例文本

```
a 50 	baidu
d 2000 	ali
e 50 	google
c 30 	byte
b 100 	jd
```

直接用`sort`排序

```
$ sort data.txt
a 50 	baidu
b 100 	jd
c 30 	byte
d 2000 	ali
e 50 	google
```

使用`-k`选项指定排序第二行，不指定结束字段的话默认从指定字段扩展到行尾，也就是说`-k 2`在下面的第一个示例排序中其实是等价于`-k 2,3`的。

但是当增加了`-n`选项时，不会默认扩展到行尾，也就是说`-k 2n`不等价于`-k 2,3n`、`-k 2n,3`、`-k 2n,3n`，这是因为按数值排序则程序只能识别数字前的符号`-`，除此之外当遇到其他非数字时就会终止此次排序。因此`-k 2n`等价于`-k 2,2n`、`-k 2n,2`、`-k 2n,2n`。

```bash
$ sort -k 2 data.txt	#这里第二列按字符顺序进行了排序，使用-n选项排序才符合预期
b 100 	jd
d 2000 	ali
c 30 	byte
a 50 	baidu
e 50 	google
$ sort -k 2n data.txt	#也可以写为 sort -k 2 -n data.txt
c 30 	byte
a 50 	baidu
e 50 	google
b 100 	jd
d 2000 	ali
```

指定字段字符进行排序

```bash
$ sort -k 3.2 data.txt
d 2000 	ali
a 50 	baidu
c 30 	byte
e 50 	google
b 100 	jd
$ sort -k 3.2,3.3 data.txt #对第三列的第二个字符开始到第三个字符进行排序
```



**uniq命令的选项参数**

```bash
-c		--count							在每一行前显示该行出现的次数
-d		--repeated					仅显示重复出现的行
-f n	--skip-fileds=num		忽略每行的前n个字段，字段间以空格分开（uniq命令无法指定分隔符）
-i		--ignore-case				行与行之间比较时忽略大小写
-s c	--skip-chars=chars	忽略每行的前c个字符
-u		--unique						仅输出不重复的行，此选项是默认的
```

测试文本

```
aaa
bbb
aaa
ccc
aaa
eee
ddd
```

`uniq`命令基本要配合`sort`命令，在排序好文件之后使用。先单独尝试一下效果

```bash
$ uniq -c data.txt
1 aaa
1 bbb
1 aaa
1 ccc
1 aaa
1 eee
1 ddd
# 每一行前都标注了出现一次，但是并没有进行统计
$ sort data.txt | uniq -c
3 aaa
1 bbb
1 ccc
1 ddd
1 eee
# 排序并进行了出现次数统计
```

与之类似，`-u`参数只能去除相邻的重复行，`-d`参数只能输出相邻的重复行。

**使用案例**

获取系统中最常使用前十个命令以及使用的总次数

```
$ history | awk -F " " '{print $2}' | sort | uniq -c | sort -k 1bnr | head -10
 234 ls
 115 git
  92 cd
  54 echo
  51 ssh
  41 vim
  40 cat
  34 rm
  27 mv
```

- 首先将`history`的输出管道到`awk`
- `awk`指定以空格为分隔符，输出第二列（也就是具体命令列）
- 管道到`sort`进行排序
- 之后将结果管道到`uniq`为每条命令进行计数
- 然后继续将结果管道到`sort`根据第一列按数值倒序排序
- 最后利用`head`取结果的前十条