---
title: shell表达式判断
date: 2020-03-12
tags: Linux
---

> test expression
>
> [ expression ]

<!--more-->

**测试文件的表达式**

| 表达式          | 为true的条件                                                 |
| --------------- | ------------------------------------------------------------ |
| file1 -ef file2 | file1与file2两个文件通过硬连接指向同一个文件                 |
| file1 -nt file2 | file1比file2新                                               |
| file1 -ot file2 | file1比file2旧                                               |
| -b file         | file存在且是一个块（设备）文件                               |
| -c file         | file存在且是一个字符（设备）文件                             |
| -d file         | file存在且是一个目录                                         |
| -e file         | file存在                                                     |
| -f file         | file存在且是一个普通文件                                     |
| -g file         | file存在且设置了组ID                                         |
| -G file         | file存在且属于有效组ID                                       |
| -k file         | file存在且有粘滞位属性                                       |
| -L file         | file存在且是一个符号链接                                     |
| -O file         | file存在并且属于有效用户ID                                   |
| -p file         | file存在并且是一个命名管道                                   |
| -r file         | file存在且可读                                               |
| -s file         | file存在且其长度大于0                                        |
| -S file         | file存在且是一个网络套接字                                   |
| -t fd           | fd是一个定向到终端/从终端定向的文件描述符，可以用来确定标准输入/输出/错误是否被重定向 |
| -u file         | file存在且设置了setuid位                                     |
| -w file         | file存在且可写                                               |
| -x file         | file存在且可执行                                             |



**测试字符串的表达式**

| 表达式                                   | 为true的条件                     |
| ---------------------------------------- | -------------------------------- |
| string                                   | string不为空                     |
| -n string                                | string长度大于0                  |
| -z string                                | string长度等于0                  |
| string1 = string2<br/>string1 == string2 | 相等                             |
| string1 != string2                       | 不等                             |
| string1 > string2                        | 经过排序后，string1在string2之后 |
| string1 < string2                        | 经过排序后，string1在string2之前 |



**测试数值的表达式**

| 表达式                | 为true的条件               |
| --------------------- | -------------------------- |
| integer1 -eq integer2 | integer1 等于 integer2     |
| integer1 -ne integer2 | integer1 不等于 integer2   |
| integer1 -le integer2 | integer1 小于等于 integer2 |
| integer1 -lt integer2 | integer1 小于 integer2     |
| integer1 -ge integer2 | integer1 大于等于 integer2 |
| integer1 -gt integer2 | integer1 大于 integer2     |

