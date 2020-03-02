---
title: docker build 的上下文目录
date: 2020-03-02
tags: docker
---

```bash
$ docker build -t test:1 .
```

这条命令最后的`.`指当前目录，`docker`引擎会解析目录下名为`Dockerfile`的文件并执行其中的命令。也可以指定特定文件：

```bash
$ docker build -t test:1 -f <filepath> .
```

但其实这里的`.`实际上指的是`docker`引擎执行构建命令时的上下文目录。

执行这条命令后首先会将此指定的目录下的所有内容打包发送给`docker`引擎，也就是说这个上下文目录与`Dockerfile`目录可以分开指定。

为了避免发送不必要的文件，可以使用`.dockerignore`来添加忽略文件。