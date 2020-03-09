---
title: shell的变量扩展
date: 2020-03-09
tags: Linux
---

## 基本扩展

```bash
$ val=test
$ echo $val
test
$ echo ${val}1
test1
```

`${val}`的形式可以明确界定变量名范围，比如上例用于拼接字符串。

<!--more-->

## 空变量的扩展

`${val1:-val2}`，当`val1`未定义或为空时，将其扩展为`val2`的值，但并不改变`val1`；若`val1`已定义且非空，则扩展为`val1`的值。

```bash
$ echo $val1 	#val1未定义

$ echo ${val1:-"test"}
test
$ echo $val1  #仍为空

```



`${val1:=val2}`，当`val1`未定义或为空时，将其扩展为`val2`的值，并且将`val2`的值赋给`val1`；若`val1`已定义且非空，则扩展为`val1`的值。

```bash
$ echo $val1 	#val1未定义
$ echo ${val1:-"test"}
test
$ echo $val1  #val1被设为test
test
```

*注意：这种方式不能给位置参数以及其他特殊参数赋值*



`${val1:?val2}`，当`val1`未定义或为空时，将`val2`的值输出到标准错误；若`val1`已定义且非空，则扩展为`val1`的值。

```bash
$ echo ${val1:?"val1 is empty"}
-bash: val1: val1 is empty
$ echo $?
1
```



`${val1:+val2}`，当`val1`未定义或为空时，不进行扩展；若`val1`已定义且非空，则将其扩展为`val2`的值，并且不改变`val1`。

```bash
$ echo $val1
#空
$ echo ${val1:+"test2"}
#空
$ val1=test1
$ echo ${val1:+"test2"}
test2
$ echo $val1
test1 	#val1的值没有改变
```

## 返回变量名扩展

`${!word*}`与`${!word@}`，这两种形式的作用相同，返回以`word`为开头的所有变量名。

```bash
$ echo ${!val*}
val1
$ val2=test2
$ echo ${!val@}
val1 val2
```

## 更多字符串扩展

```bash
# ${#str}，其作用是扩展为`str`变量包含的字符串长度。
$ echo ${#val}
0
$ val=test
$ echo ${#val}
4
```



`${str:offset:length}`，其作用为，从索引`offset`处开始截取`str`，截取长度为`length`，如过没有设置`length`，则一直截取到末尾。

如果`offset`为负数，则从字符串末尾倒数`offset`开始进行截取，并且在`offset`前必须添加一个空格，否则会被混淆为`${str:-"val"}`这种扩展形式。

```bash
$ str="test string"
$ echo ${str:2}
st string
$ echo ${str:2:4}
st s
$ echo ${str: -5:3}
tri
```

如果参数是`@`，扩展的结果是从`offset`开始，`length`为位置参数。



`${str#pattern}`与`${str##pattern}`，作用为从*字符串开头*开始去除字符串`str`中满足模式`pattern`的部分，这两种方式的区别是`#`去除最短匹配，而`##`去除最长匹配。

```bash
$ str="test.tar.gz"
$ echo ${str#*.}		#去除最短匹配
tar.gz
$ echo ${str##*.}		#去除最长匹配
gz
```



`${str%pattern}`与`${str%%pattern}`，作用为从*字符串末尾*开始去除字符串`str`中满足模式`pattern`的部分，这两种方式的区别是`%`去除最短匹配，而`%%`去除最长匹配。

```bash
$ str="test.tar.gz"
$ echo ${str%.*}		#去除最短匹配
test.tar
$ echo ${str%%.*}
test
```



`${str/pattern/replace}`，其作用为将字符串`str`中第一个符合`pattern`模式匹配的部分替换为`replace`。

```bash
$ str="test.txt.txt"
$ echo ${str/test/new-name}
new-name.txt.txt
$ echo ${str/txt/suffix}
test.suffix.txt
```

`${str//pattern/replace}`，其作用为将字符串`str`中所有符合`pattern`模式匹配的部分替换为`replace`。

```bash
$ echo ${str//txt/suffix}
test.suffix.suffix
```

`${str/#pattern/replace}`，`/#`的形式要求匹配出现在字符串开头。

```bash
$ echo ${str/#txt/suffix}
test.txt.txt		#txt不在字符串开头，所以没有替换
$ echo ${str/#test/new-name}
new-name.txt.txt
```

`${str/%pattern/replace}`，`/%`的形式要求匹配出现在字符串末尾。

```bash
$ echo ${str/%test/new-name}	
test.txt.txt		#test不在字符串末尾，所以没有替换
$ echo ${str/%txt/suffix}
test.txt.suffix
```

如果不设置`replace`，这四种形式的扩展将去掉满足匹配模式`pattern`的部分。

## 数值的扩展

数值计算的扩展使用`$(())`或`$[]`。

```bash
$ echo $((1+2*3))
7
$ echo $[1+2*3]
7
```

进制转换

```bash
$ echo $((2#1111))
15
$ echo $((16#ab))
171
$ echo $[2#1111]		#不支持进制转换
-bash: $: 未找到命令
```



