---
title: 树莓派系统安装及设置ssh开机启动
permalink: raspberry-ssh
date: 2020-03-11
tag: [Linux, 树莓派]
---

**资源及工具**

- 国内在官网下载系统镜像会很慢，经过搜索一番之后找到此[高速资源](https://www.jianshu.com/p/8446f7971d66)。

- 如需格式化 SD 卡可使用这款[工具](https://www.sdcard.org/downloads/index.html)。

- 使用 win32DiskImager 将系统写入 SD 卡



**开启ssh服务**

开机尝试`ssh`登陆，请求被拒绝，对于这个问题有一些旧答案说需要在`/boot`分区下创建一个名为`ssh`的空白文件，并尝试重`sshd`服务。而实际上新版本的系统已经不需要这样设置了，只需要开启`ssh`服务。

```bash
$ systemctl start sshd
//Unit sshd.service not found
```

这条命令会报错。树莓派系统`ssh`服务名并不是`sshd`，而是`ssh`。

```bash
$ systemctl start ssh
```

启动成功



**设置ssh开机自启**

这里只需要修改文件`/etc/rc.local`文件

```bash
...
/etc/init.d/ssh start  #加入这一行
exit 0
```

