---
title: Linux下的SUID、SGID、SBIT权限设置与作用
permalink: linus-uid-gid-bit
date: 2020-03-13
tags: Linux
---

## SUID

`SUID`只能对文件设置

- 只对二进制可执行文件有效
- 执行者对文件有`x`权限，在执行此程序期间获取到程序所有者的身份

普通的文件权限

```
-rwxrwxr-x
```

设置了`SUID`位的文件权限，拥有者的`x`变为`s`。

```
-rwsrwxr-x
```

设置方法

```
chmod 4xxx <name>
```

<!--more-->

做一个实验验证，创建两个测试用户`test-1 test-2`，登陆到`test-1`并在家目录创下创建一个文件`data.log`。

```bash
============
test-1 data
============
```

切换到`/tmp`目录，建立一个读取`data.log`的脚本，并为其分配`755`权限。

```bash
#!/bin/bash
cat /home/test-1/data.log
```

```bash
$ chmod 755 readlog.sh
```

现在切换到`test-2`用户，执行一下这个脚本看一下

```bash
$ su test-2
$ /tmp/readlog.sh
cat: /home/test-1/data.log: Permission denied
```

肯定是没有权限读取的，切回`test-1`用户，给脚本设置`SUID`位，然后再切回`test-2`并执行脚本

```bash
$ su test-1
$ chmod 4755 readlog.sh
$ su test-2
$ /tmp/readlog.sh
$ cat: /home/test-1/data.log: Permission denied
```

此时依然无法读取，说明对于`shell`脚本，`SUID`的设置是没有作用的。



这里我切到`test-1`写一段`go`程序，并编译成二进制文件。

```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {
    fmt.Println("readlog is running...")
    data, err := ioutil.ReadFile("/home/test-1/data.log")
    if err != nil {
        fmt.Println("File reading error", err)
        return
    }
    fmt.Println("Contents of file:", string(data))
}
```

```bash
$ go build readlog.go
```

切回`test-2`，执行这个二进制文件，没有权限。

```
$ /tmp/readlog
readlog is running...
File reading error open /home/test-1/data.log: permission denied
```

我们切回`test-1`并给这个二进制文件设置`SUID`位，再切回`test-2`执行，已经可以读取到`test-1`用户家目录下的文件了，说明这个执行过程中`test-2`用户获取到了`test-1`的身份。

```bash
$ chmod 4755 readlog
$ su test-2
$ /tmp/readlog
readlog is running...
Contents of file: test-1 data
```

## SGID

`SGID`可以对文件或目录设置

- 文件
  - 对二进制文件有效
  - 执行者对文件有`x`权限，在执行期间获取到程序所属群组的权限

- 目录
  - 对目录有`r`和`x`权限的用户可以进入目录
  - 用户在此目录下的有效群组将会变为该目录的群组
  - 若用户在此目录具有`w`权限，则其创建的文件的所属群组与此目录的所属群组相同

普通文件权限

```
-rwxrwxr-x
```

设置了`SGID`权限，所属组的`x`变为`s`

```
-rwxrwsr-x
```

设置方法

```
chmod 2xxx <name>
```



建立一个测试群组，创建一个目录并分配到这个群组上

```bash
$ groupadd sgid-test
$ mkdir /tmp/sgid-test
$ chgrp /tmp/sgid-test sgid-test
$ ls /tmp/
drwxr-xr-x. 2 root   sgid-test       6 3月  14 16:10 sgid-test
```

切换到`test-1`在这个目录创建一个文件试试看

```bash
$ su test-1
$ echo "test1" > /tmp/sgid-test/data.txt
$ ls /tmp/sgid-test/
-rw-rw-r--. 1 test-1 test-1 4 3月  14 16:14 data.txt
```

这时创建的文件属于`test-1`用户和`test-1`分组

切回管理员账户并给改目录设置`SGID`，再用`test-1`创建一个文件试一下

```bash
$ su -
$ chmod 2777 /tmp/sgid-test
$ ls /tmp/
drwxrwsrwx. 2 root   sgid-test      22 Mar 14 16:14 sgid-test
$ su test-1
$ echo "test2" > /tmp/sgid-test/data2.txt
$ ls /tmp/sgid-test/
-rw-rw-r--. 1 test-1 sgid-test 4 Mar 14 16:15 data2.txt
```

可以看到这时`test-1`创建的文件属于`test-1`用户和`sgid-test`分组

## SBIT

`SBIT`只能对目录设置，设置了该权限之后，对此目录有`w`和`x`权限的用户，在该目录内创建的文件之后用户自己与`root`有权限删除。

```
drwxrwxrwt.   8 root root       177 Mar 14 16:55 tmp
```

`test-1`在`/tmp`创建文件并给予全部权限，再切到`test-2`对其进行一些操作

```bash
$ echo "test" > /tmp/test.txt
$ chmod 777 /tmp/test.txt
$ su test-2
$ echo "line2" >> /tmp/test.txt 	#可以正常编辑
$ rm /tmp/test.txt
rm: cannot remove ‘test.txt’: Operation not permitted		#但是不能删除
```

## 空权限

```bash
$ ls -l
-rw-r--r--. 1 root root 4 Mar 14 17:02 s-test
```

此文件的所有者、所属组及其他人均没有`x`权限，此时设置`SUID`、`SGID`、`SBIT`来看一下

```bash
$ chmod 7644 s-test
$ ls -l
$ -rwSr-Sr-T. 1 root root 4 Mar 14 17:02 s-test
```

可以发现对于没有`x`权限的文件设置这三种属性，则`x`权限位分别变为`S`、`S`和`T`