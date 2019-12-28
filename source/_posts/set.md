---
title: set 命令及常用参数
date: 2019-12-28
tag: linux
---

> **set命令**作用主要是显示系统中已经存在的shell变量，以及设置shell变量的新变量值。使用set更改shell特性时，符号"+"和"-"的作用分别是打开和关闭指定的模式。set命令不能够定义新的shell变量。如果要定义新的变量，可以使用[declare](http://man.linuxde.net/declare)命令以`变量名=值`的格式进行定义即可。

<!--more-->

## 常用参数

### -u 参数

执行时遇到未定义的变量 `bash` 默认忽略，设置 `-u` 参数当执行使用到未定义的变量时显示错误信息。

```shell
#!/bin/bash

echo $a
echo "test"
```

```bash
$ bash set.sh

test
```

设置 `-u` 参数

```shell
#!/bin/bash
set -u

echo $a
echo "test"
```

```bash
$ bash set.sh
set.sh:行4: a: 未绑定变量
```

等效命令

```bash
$ set -o nounset
```

### -x 参数

脚本运行时，打印出输出很多时候不能确定是由哪条命令输出的，设置 `-x` 参数在执行命令后会先显示该命令及参数，然后打印输出，有利于调试。

```shell
#!/bin/bash

echo "a"
echo "test"
```

```bash
$ bash set.sh
a
test
```

设置 `-x` 参数

```shell
#!/bin/bash
set -x

echo "a"
echo "test"
```

```bash
$ bash set.sh
+ echo a
a
+ echo test
test
```

等效命令

```bash
$ set -o xtrace
```

### -e 参数

```bash
#!/bin/bash

t
echo test
```

```bash
$ bash set.sh
set.sh:行3: t: 未找到命令
test
```

以上脚本虽然在第三行 `t` 命令不存在，但是报错后继续向下执行。为错误的叠加有时候需要发生错误时推出脚本。此时设置 `-e` 参数。

```shell
#!/bin/bash
set -e

t
echo test
```

```bash
$ bash set.sh
set.sh:行4: t: 未找到命令
```

此时在报错之后直接退出不会向下执行。

也可以在给一个代码段开启 `-e` 参数，执行完改代码段后再取出改参数。

```shell
#!/bin/bash
set -e

t
echo test

set +e
```

等效命令

```bash
$ set -o errexit
```

## 其他参数

```ini
-a：标示已修改的变量，以供输出至环境变量。
-b：使被中止的后台程序立刻回报执行状态。
-C：转向所产生的文件无法覆盖已存在的文件。
-d：Shell预设会用杂凑表记忆使用过的指令，以加速指令的执行。使用-d参数可取消。
-f：取消使用通配符。
-h：自动记录函数的所在位置。
-H Shell：可利用"!"加<指令编号>的方式来执行history中记录的指令。
-k：指令所给的参数都会被视为此指令的环境变量。
-l：记录for循环的变量名称。
-m：使用监视模式。
-n：只读取指令，而不实际执行。
-p：启动优先顺序模式。
-P：启动-P参数后，执行指令时，会以实际的文件或目录来取代符号连接。
-t：执行完随后的指令，即退出shell。
-v：显示shell所读取的输入值。
```