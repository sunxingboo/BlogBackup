---
title: golang的异常处理 
permalink: go-panic
date: 2020-04-05
tags: Go
---

![](https://blog-static-resources.oss-cn-beijing.aliyuncs.com/blogImg/golang.jpg)

<!--more-->

## panic

```go
// The panic built-in function stops normal execution of the current
// goroutine. When a function F calls panic, normal execution of F stops
// immediately. Any functions whose execution was deferred by F are run in
// the usual way, and then F returns to its caller. To the caller G, the
// invocation of F then behaves like a call to panic, terminating G's
// execution and running any deferred functions. This continues until all
// functions in the executing goroutine have stopped, in reverse order. At
// that point, the program is terminated with a non-zero exit code. This
// termination sequence is called panicking and can be controlled by the
// built-in function recover.
func panic(v interface{})
```

内建函数`panic`的作用是停止当前协程的正常执行。

- 当在函数`F`内调用`panic`时，`F`的正常执行立即停止

- 执行`defer`声明的动作，然后`F`返回其调用者（假设调用者为`G`函数）
- 此时在`G`中，对`F`的调用就如同调用`panic`，继而终止`G`的执行并且开始指定`defer`
- 接下来继续向上返回，直到协程中的所有函数都被终止执行
- 程序以一个非 0 的状态码退出

这个终止流被称为`panicking`，可以通过内建函数`recover`来进行控制。



```go
package main
import "fmt"
func main() {
	fmt.Println("main func start")
	f1()
	fmt.Println("main func end")
}
func f1()  {
	fmt.Println("f1 func start")
	panic("f1 func panic")
}
//main func start
//f1 func start
//panic: f1 func panic
```

## recover

```go
// The recover built-in function allows a program to manage behavior of a
// panicking goroutine. Executing a call to recover inside a deferred
// function (but not any function called by it) stops the panicking sequence
// by restoring normal execution and retrieves the error value passed to the
// call of panic. If recover is called outside the deferred function it will
// not stop a panicking sequence. In this case, or when the goroutine is not
// panicking, or if the argument supplied to panic was nil, recover returns
// nil. Thus the return value from recover reports whether the goroutine is
// panicking.
func recover() interface{}
```

内建函数`recover`允许程序管理一个发生了`panicking`的协程的行为。

在`defer`函数（而不是其调用的函数）中调用`recover`函数，通过恢复正常的执行来停止`panicking`序列，并检索传递给`panic`的错误值。

如果在`defer`之外调用`recover`则不会停止`panicking`序列。

当在协程内没有发生`panic`，或者传递给`panic`参数为`nil`，则`recover`的返回值为`nil`。

因此`recover`的返回值就表明了协程是否发生了`panic`。



捕获`panic`并恢复程序的正常执行。

```go
package main
import "fmt"
func main() {
	fmt.Println("main func start")
	f1()
	fmt.Println("main func end")
}
func f1()  {
	fmt.Println("f1 func start")
	defer func() {
		if err := recover(); err != nil {
			fmt.Println("f1 catch err: ", err)
		}
	}()
	//panic("f1 func panic")
	f2()
	fmt.Println("f1 func end")
}
func f2()  {
	fmt.Println("f2 func start")
	defer func() {
		if err := recover(); err != nil {
			fmt.Println("catch err: ", err)
		}
	}()
	panic("f2 func panic")
	fmt.Println("f2 func end")
}
//main func start
//f1 func start
//f2 func start
//catch err:  f2 func panic
//f1 func end
//main func end
```

捕捉到当前层的`panic`，恢复上一层的正常执行。

## defer

`defer`的三个特性：

- 声明`defer`时实时解析参数
- 多个`defer`的执行顺序为先进后出
- 可以读取并改变有名返回参数的值

```go
package main
import "fmt"
func main() {
    i := 1
    defer fmt.Println("i= ", i)
    i += 1
}
// i= 1
```

这里输出的结果为`i= 1`，因为声明`defer`时，后面的参数立即被解析，所以传入的`i`是 1，之后的增量不会被捕获到。



```go
package main
import "fmt"
func main() {
    defer fmt.Println("first defer")
    defer fmt.Println("second defer")
}
//second defer
//first defer
```

先声明的`defer`后执行。



```go
package main
import "fmt"
func main() {
    fmt.Println(f1())
}
func f1() (i int) {
    defer func() {
        i++
    }()
    return 10
}
//11
```

`return`不是原子操作。

当返回值有名时，返回值相当于引用赋值，所以可以被`defer`修改。而匿名返回值相当于拷贝赋值，不会被修改。

```go
package main
import "fmt"
func main() {
    fmt.Println(f1())
}
func f1() int {
    var i int
    defer func() {
        i = 7
    }()
    i = 10
    return i
}
//10
```

