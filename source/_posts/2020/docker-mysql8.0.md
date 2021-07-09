---
title: docker启动mysql8.0服务遇到的问题记录
permalink: docker-mysql8
date: 2020-05-20
tags: [docker, mysql]
---

![](https://blog-static-resources.oss-cn-beijing.aliyuncs.com/blogImg/docker.jpg)

<!--more-->

启动容器

```
docker run -p 3306:3306 --name mysql-server \
-v /data/mysql/conf:/etc/mysql \
-v /data/mysql/logs:/var/log/mysql \
-v /data/mysql/data:/var/lib/mysql \
-v  /data/mysql/mysql-files:/var/lib/mysql-files \
-e MYSQL_ROOT_PASSWORD= \
-d mysql
```

此时可能从外部无法连接容器内的mysql

```
alter user 'root'@'%' identified with mysql_native_password by 'password';
```

可能遇到如下问题或其他错误：

```
Access denied for user 'root'@'localhost' (using password: YES)
```

解决方法

```
update user set authentication_string = 'root' where user = 'root' and host = '%';
update user set authentication_string = 'root' where user = 'root' and host = 'localhost';
```

使用`sequel pro`进行连接时，报如下错误：

```
MySQL said: Authentication plugin 'caching_sha2_password' cannot be loaded: dlopen(/usr/local/mysql/lib/plugin/caching_sha2_password.so, 2): image not found
```

原因

mysql8之前的版本中加密规则是```mysql_native_password```，而mysql8之后，加密规则是```caching_sha2_password```，而数据库客户端无对应的驱动。

解决方法

登录到容器中，修改加密规则并更新用户密码，刷新权限。

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;   #修改加密规则 
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';   #更新一下用户的密码
FLUSH PRIVILEGES;   #刷新权限 
```

此时再次尝试连接，成功。

