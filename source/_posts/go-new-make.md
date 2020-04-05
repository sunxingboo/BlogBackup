---
title: Golang中new()与make()的作用与区别
date: 2020-04-04
tags: Go
---

![golang](https://blog-static-resources.oss-cn-beijing.aliyuncs.com/blogImg/golang.jpg)

<!--more-->

先看一下源码中这两个函数的说明

## new

```go
// The new built-in function allocates memory. The first argument is a type,
// not a value, and the value returned is a pointer to a newly
// allocated zero value of that type.
func new(Type) *Type
```

内置函数`new`分配内存。

第一个参数是一个类型，而不是值，这个函数会分配一个这个类型的零值，并返回这个零值的指针。

```go
package main
import (
	"fmt"
	"reflect"
)
type TestNew struct {
	str string
}
func main() {
	t1 := new(TestNew)
	t2 := &TestNew{}
	fmt.Println(reflect.TypeOf(t1), reflect.TypeOf(t2))
}
// *main.TestNew *main.TestNew
```

这两种创建变量的方式作用是相同的。

## make

```go
// The make built-in function allocates and initializes an object of type
// slice, map, or chan (only). Like new, the first argument is a type, not a
// value. Unlike new, make's return type is the same as the type of its
// argument, not a pointer to it. The specification of the result depends on
// the type:
//	Slice: The size specifies the length. The capacity of the slice is
//	equal to its length. A second integer argument may be provided to
//	specify a different capacity; it must be no smaller than the
//	length. For example, make([]int, 0, 10) allocates an underlying array
//	of size 10 and returns a slice of length 0 and capacity 10 that is
//	backed by this underlying array.
//	Map: An empty map is allocated with enough space to hold the
//	specified number of elements. The size may be omitted, in which case
//	a small starting size is allocated.
//	Channel: The channel's buffer is initialized with the specified
//	buffer capacity. If zero, or the size is omitted, the channel is
//	unbuffered.
func make(t Type, size ...IntegerType) Type
```

内置函数`make`分配并初始化一个`slice`、`map`或者`chan`对象（只支持这三种）。

与`new`函数相似，第一个参数是一个类型，而不是值。

与`new`不同的是，`make`函数的返回值的类型与它的参数指定的类型相同，而不是指向这个类型的指针。

其返回值依赖于具体传入的类型：

- `slice`：`size`指定切片的长度。第二个`int`参数可以指定切片长度。第三个参数指定切片容量，这个值必须不小于长度。
- `map`：根据`size`指定的数量来分配足够的空间容纳元素的空`map`。忽略`size`参数则会默认分配一个较小的初始长度的`map`。
- `channel`：通道的缓冲区通过指定的缓冲区容量来初始化。如果`size`参数值为 0 或者被省略，则这个通道是没有缓冲区的（简单举例就是写入一个数据就会进入阻塞状态）。



初始化`map`和`channel`时，可以忽略`size`参数

```go
make(map[string]int)
make(chan int)
```

初始化`slice`时必须指定长度，容量可以忽略。

```go
make([]int)       //错误
make([]int, 2)    //长度和容量均为2
make([]int, 2, 3) //长度为2，容量为3
```

初始化`channel`时指定缓冲区容量

```go
make(chan int, 3) 
```

对于`slice`和`channel`均可以使用内置函数`len`获取长度，使用`cap`获取容量，而对于`map`不能使用`cap`函数。



`map`是没有大小或长度的限制的。比如初始化一个指定容量的`map`：

```go
m := make(map[string]int, 1)
m["t1"] = 1
m["t2"] = 2 //此时已经超出指定的容量1，会动态的调整大小
```

频繁的调整会影响性能，因此当定义一个元素数量的增长趋势比较大的`map`时。应该大致的预定义一个容量，而不是让它频繁的触发容量调整。



