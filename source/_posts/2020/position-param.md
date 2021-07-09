---
title: shell位置参数
permalink: shell-params
date: 2020-03-08
tags: Linux
---

`shell`提供了一组变量，用来存储传递给脚本或函数的参数

<!--more-->

## $0~$9以及更多的位置参数扩展

```shell
#!/bin/bash

# posit-param
echo -e "\$0 = $0"
echo -e "\$1 = $1"
echo -e "\$2 = $2"
echo -e "\$3 = $3"
echo -e "\$4 = $4"
echo -e "\$5 = $5"
echo -e "\$6 = $6"
echo -e "\$7 = $7"
echo -e "\$8 = $8"
echo -e "\$9 = $9"
```

执行这个脚本，输出如下

```shell
$ ./posit-param
$0 = ./posit-param.sh
$1 =
$2 =
$3 =
$4 =
$5 =
$6 =
$7 =
$8 =
$9 =
```

`$0`存储的是所执行的程序所在的完成路径名，没有传递其他参数，则其他位置参数为空



在脚本内增加一行接收第十个参数

```shell
...
echo -e "\$10 = $10"
```

执行脚本，传递十个参数

```shell
$ ./posit-param a b c d e f g h i j
$0 = ./posit-param.sh
$1 = a
$2 = b
$3 = c
$4 = d
$5 = e
$6 = f
$7 = g
$8 = h
$9 = i
$10 = a0  //没有接收到参数
```

可以通过参数扩展来接收第十个及更多的参数，将最后一行改为

```shell
...
echo -e "\$10 = ${10}" #这样可以正常获取参数输出啦
```

## $#

`$#`存储的是传递给脚本或者函数的参数的个数

在`posit-param`脚本内加一行代码

```shell
...
echo -e "params number is $# \n"
...
```

```bash
$ ./posit-param a b c d e f g h i j
params number is 10
...
```

## shift用于处理大量参数

`shift`命令的作用是把除了`$0`以外的全部位置参数向左移动指定的间隔

```shell
...
shift 	#左移一位，$1被丢弃，$2移到$1，$3移到$2...
shift 2 #左移两位，$1与$2被丢弃，$3移到$1，$4移到$2
...
```

示例脚本如下

```shell
#!/bin/bash

# posit-param2
count=1
while [ $# -ne 0 ]; do
	echo "param $count is $1"
	count=$(( count + 1 ))
	shift
done
```

```bash
$ ./posit-param2 a b c
param 1 is a
param 2 is b
param 3 is c
```

## $*与$@

`$*`扩展为从`1`开始的位置参数列表。当被双引号包裹时（`"$*"`），将全部位置参数扩展为双引号引用的一个字符串，每个位置参数以`IFS`（通常默认为空格）变量分隔开。

`$@`扩展为从`1`开始的位置参数列表。当被双引号包裹时（`"$@"`），将每个参数扩展为双引号引用的独立的字符串。

```shell
#!/bin/bash

# posit-param3
print_params() {
	echo "\$1 = $1"
	echo "\$2 = $2"
	echo "\$3 = $3"
	echo "\$4 = $4"
}
pass_params() {
	echo '$*:'; print_params $*
	echo '"$*":'; print_params "$*"
	echo '$@:'; print_params $@
	echo '"$@":'; print_params "$@"
}
pass_params "word" "words with spaces"
```

```bash
$ ./posit-param3
#脚本输出
$*:
$1 = word
$2 = words
$3 = with
$4 = spaces

"$*":
$1 = word words with spaces
$2 =
$3 =
$4 =

$@:
$1 = word
$2 = words
$3 = with
$4 = spaces

"$@":
$1 = word
$2 = words with spaces
$3 =
$4 =
```

所以位置参数的扩展有`$*`、`"$*"`、`$@`、`"$@"`四种形式。通常情况下，`"$@"`会更符合我们的预期效果。