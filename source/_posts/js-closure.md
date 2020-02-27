---
title: JavaScript闭包
date: 2020-02-27
tags: JavaScript
---

闭包就是获取其他函数内部变量的函数。

<!--more-->

## 概念理解

```javascript
function f1() {
    let n = 7;
}
f1();
console.log(n);  // ReferenceError: n is not defined
```

函数外部无法获取函数内部变量，并且函数执行完毕后变量已被销毁。

```javascript
function f1() {
    let n = 7;
    return function() {
        return n;
    };
}
let res = f1();
console.log(res()); // 7
```

在函数内部再定义一个函数，并作为返回值，此匿名函数依赖于`f1`，所以`f1`执行完毕后并不会被销毁回收，因此可以通过调用返回的匿名函数来获取`f1`内部变量`n`。

## this关键字

函数内部的`this`关键字指向的是调用此函数的对象。定义时并没有生效，而是在运行时生效。

```javascript
name = 'sun';
function f1() {
    let name = 'bo';
    return this.name; //这里指向顶层window对象
}
console.log(f1()); // ‘sun’
```

## 闭包示例

```javascript
var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function(){
        return function(){
            return this.name; //这里的 this 在运行时指向的是 getNameFunc 函数 
        };
    }
};
console.log(object.getNameFunc()()); // undefined
```

如上代码由于运行时`this`指向`getNameFunc`函数，此函数内没有`name`属性定义，因此返回`undefined`。

```javascript
var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function(){
        var that = this; //这里的 this 在运行指向的是 object，赋值给 that 后，在返回的闭包函数中指向的依然是 object
        return function(){
            return that.name;
        };
    }
};
console.log(object.getNameFunc()()); // "My Object"
```

通过一层转化暂存`this`指向，返回的闭包函数可以获取到`object`的`name`属性。