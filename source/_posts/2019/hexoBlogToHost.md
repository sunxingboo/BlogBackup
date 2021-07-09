---
title: hexo 博客部署到云服务器总结
permalink: hexo-to-host
date: 2019-12-21
tags: hexo
---

## 部署方式

- `rsync`
- `git`

<!--more-->

## nginx 配置

```bash
$ yum install -y nginx git
$ mkdir /data/www/hexo
$ vim /etc/nginx/nginx.conf
```

```nginx
server {
  listen       80 default_server;
  listen       [::]:80 default_server;
  server_name  codernote.top;
  root         /data/www/hexo;
  ....
}
```

为登录账户创建 `authorized_keys`

```bash
$ vim ~/.ssh/authorized_keys
#每行一个key
```

## rsync 部署

*推荐使用这种方式*

安装依赖工具

```bash
$ npm install hexo-deployer-rsync --save
```

修改配置 `_config.yml`，添加 `deploy`，这里同时部署到 `github` 和云服务器

```yaml
deploy:
  - type: git
    repository: git@github.com:sunxingboo/sunxingboo.github.io
    branch: master
  - type: rsync
    host: <域名或ip>
    user: <登录用户>
    root: <部署目录>
    port: <ssh端口>
    delete: <部署前先清空目录>
```

## git 部署

创建仓库

```bash
$ mkdir /data/GitLibrary && cd /data/GitLibrary
$ git init --bare hexo.git
```

创建钩子，在仓库更新时同步更新到博客目录

```shell
$ vim /data/GitLibrary/hexo.git/hooks/post-receive

#!/bin/bash
git --work-tree=/data/www/hexo --git-dir=/data/GitLibrary/hexo.git checkout -f
```

开放可执行权限

```bash
$ chmod +x /data/GitLibrary/hexo.git/hooks/post-receive
```

修改 `_config.yml` 

```yaml
deploy:
  - type: git
    repository: user@IP:/data/GitLibrary/hexo.git
    branch: master
```

这里如果出于安全原因修改了 `ssh` 端口，则配置如下

```yaml
deploy:
  - type: git
    repository: ssh://user@IP:port/data/GitLibrary/hexo.git
    branch: master
```

## 部署

```bash
$ hexo d -g
```





