---
title: go mod的使用
permalink: go-mod
date: 2020-04-19 
tags: Go
---

![](https://blog-static-resources.oss-cn-beijing.aliyuncs.com/blogImg/go-mod.png)

<!--more-->

在 2019 年刚接触 Go 的时候，项目中包的引入是采用`go get`下载到`src`目录的方式。此时依赖包与项目处于同级目录。

今年来到新公司，主要从事 Go 的开发，项目的包管理采用 `go mod`的方式，也是比较新的版本才提供的官方包管理机制，大致做一下使用方法记录。

在项目根目录创建`go.mod`文件：

```bash
go mod init [模块名]
```

`require`引入依赖包：

```go
module blockchain
go 1.13
require (
	github.com/boltdb/bolt v1.3.2-0.20180302180052-fd01fc79c553
	golang.org/x/sys v0.0.0-20200413165638-669c56c373c4 // indirect
)
```

后面可以标明包的版本，可以是标签或者分支名。

执行`go mod tidy`后会拉取依赖包或清除不再使用的依赖包，存放的目录在`$GOPATH/pkg/mod/`，同一个包可以存在多个版本。

如果希望将项目的依赖包纳入版本控制，可以执行`go mod vendor`，在项目根目录下生成`vendor`目录并存放依赖包。



如果后续需要替换依赖包，可以使用`replace`关键字：

```
module blockchain
go 1.13
require (
	github.com/boltdb/bolt v1.3.2-0.20180302180052-fd01fc79c553
	golang.org/x/sys v0.0.0-20200413165638-669c56c373c4 // indirect
)
replace github.com/boltdb/bolt => github.com/AAA/BBB
```



使用 Goland 时，需要在偏好设置中开启 Go Module 设置，否则程序虽然可以正常运行，但是 IDE 中导入的包依然会显示为错误的红色：

![](https://blog-static-resources.oss-cn-beijing.aliyuncs.com/contentImg/%E6%88%AA%E5%B1%8F2020-04-19%E4%B8%8B%E5%8D%886.30.46.png)