---
title: xargs 命令及常用参数
date: 2019-12-20 17:13
tags: Linux
---

> xargs - build and execute command lines from standard input

<!--more-->

## 管道命令

有的命令可以将标准输入作为参数

```bash
$ echo "test" | grep t
test
```

但是很多命令不接受标准输入作为参数，如 ```echo```

```bash
$ echo "test" | echo
// 不会有输出
```

## xargs 的作用

```xargs``` 可以为命令接受标准输入并转化为参数列表，它的默认命令就是 ```echo```

```bash
$ echo "test" | xargs 
test
```

上面的 ```xargs``` 命令将标准输入转换为命令行参数传给默认命令 ```echo```

命令语法

```bash
$ xargs [-options] [command]
```

##  -d 参数设置分隔符

```xargs``` 默认将空格和换行符作为分隔符

```bash
$ echo "ababa" | xargs -d "a"
b b
```

## -p 与 -t 参数

```-p``` 在命令执行前打印命令，并询问用户是否要执行

```bash
$ echo "a b c" | xargs -p
echo a b c ?...
```

命令将打印如上结果，用户输入 ```y/Y``` 才会执行

```-t``` 参数是在打印将要执行的命令之后立即执行该命令，不需要用户确认

```bash
$ echo "a b c" | xargs -t mkdir
mkdir a b c
```

执行上述命令创建三个目录

## -L 参数

标准输入包含多行时，```-L``` 参数指定将多少行作为一个命令行参数，根据构建的命令参数个数执行一到多次命令

```bash
#test.txt
one two 
1 2
I II
```

```bash
$ cat text.txt | xargs -t -L 1
echo one two
one two
echo 1 2
1 2
echo I II
I II
```

```bash
$ cat text.txt | xargs -t -L 2
echo one two 1 2
one two 1 2
echo I II
I II
```

## -n 参数

```-n``` 参数指定将多少项作为一个命令行参数

```bash
$ cat text.txt | xargs -t -n 4
echo one two 1 2
one two 1 2
echo I II
I II
```

## -I 参数

```-I``` 参数为多个命令绑定参数

```bash
$ cat text.txt | xargs -I param sh -c "echo param;touch param"
one two
1 2
I II
```

命令输除如上结果，并创建对应的 6 个文件











