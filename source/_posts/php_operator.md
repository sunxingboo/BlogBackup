---
title: PHP ?? 与 ?: 操作符的区别
date: 2020-01-07 
tag: PHP
---

`??` 操作符是 PHP 7 引入的特性，它的使用方式与 `?:` 的区别总结如下：

```php
<?php
  $str = "";
	$arr = ["b" => 0, "c" => null];
	$test1 = $str ?? "test1";
	$test2 = $str ?: "test2";
	$test3 = $arr["a"] ?? "test3";
	$test4 = $arr["b"] ?? "test4";
	$test5 = $arr["c"] ?? "test5";
	var_dump($test1, $test2, $test3, $test4, $test5);
```

```
string(0) ""
string(5) "test2"
string(5) "test3"
int(0)
string(5) "test5"
```

<!--more-->

`??` 的判断形式相当于 `isset` 函数，即：

```php
//$test1 = $str ?? "test";
$test1 = isset($str) ? $str : "test";
```

`isset` 函数判断结果为 `false` 的几种情况：

- 变量未定义
- 变量值为 `null`（也就是说值为 `0` `"0"`都会返回 `true`）
- 检查字符串的**非数字**偏移量

而 `?:` 的判断相当于 `empty` 函数，即：

```php
//$test2 = $str ?: "test";
$test2 = !empty($str) ? $str : "test";
```

`empty` 函数判断结果为 `false` 的几种情况：

- 空字符串 `""`
- 整数 `0`
- 浮点数 `0.0`
- `null`
- `false`
- 空数组 `[]`
- 声明了但是没有值的变量

`isset` 和 `empty` 函数官方文档：

> isset函数文档：https://www.php.net/manual/zh/function.isset.php
>
> empty函数文档：https://www.php.net/manual/zh/function.empty.php