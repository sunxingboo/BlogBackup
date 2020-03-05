---
title: find命令使用笔记
date: 2020-03-04
tags: Linux
---

```bash
find [查找路径] [选项] 
```

<!--more-->

## -name选项

```bash
find / -name "*.txt"
```

`-name`后可以是确切的带查询名称或者模式字符串

`-iname`可以忽略大小写

`-regex`是匹配整个路径

## -size选项

```bash
$ find / -size 1033c
```

查找指定大小的文件，若不指定单位则默认的单位是`b`

| 单位符号 | 说明                                  |
| -------- | ------------------------------------- |
| c        | bytes                                 |
| w        | two-byte words                        |
| b        | 512-byte blocks                       |
| k        | Kilobytes (units of 1024 bytes)       |
| M        | Megabytes (units of 1048576 bytes)    |
| G        | Gigabytes (units of 1073741824 bytes) |
|          |                                       |

## -type选项

```bash
$ find / -type f
```

根据指定的类型查询目录或文件

| 类型标识 | 说明         |
| -------- | ------------ |
| f        | 普通文件     |
| d        | 目录         |
| l        | 链接         |
| b        | 块设备       |
| c        | 字符设备     |
| p        | Fifo管道文件 |
| s        | 套接字       |

## -user选项

```bash
$ find / -user test1
```

查找所属用户为指定用户的文件或目录

`-uid`选项查找符合指定的`uid`的文件或目录

## -group选项

```bash
$ find / -group test2
```

查找所属组为指定组的问价或目录

`-gid`选项查找符合指定的`gid`的文件或目录

## -exec选项

对找到的文档执行特定的命令