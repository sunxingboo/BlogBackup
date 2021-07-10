---
title: 摩尔投票算法
permalink: mole-vote
date: 2021-07-06
tag: [数据结构与算法]
---

先来看一道算法题：

> 数组中占比超过一半的元素称之为主要元素。给定一个**整数**数组，找到它的主要元素。若没有，返回-1
>
> **示例 1：**
>
> ```
> 输入：[1,2,5,9,5,9,5,5,5]
> 输出：5
> ```

<!--more-->

## 方法一

看到这个题目第一想法是，先将数组排序，然后取数组中间值。因为超过数组半数的元素在排序之后必然会占据中点位置。

```go
func majorityElement(nums []int) int {
    sort.Ints(nums)
    return nums[len(nums)/2]
}
```

时间复杂度为**O(nlogn)**，就是排序算法的时间复杂度。

空间复杂度为**O(1)**。

但是这个实现只能处理存在主要元素的样本。

例如当样本为`[1, 2, 3]`时，不存在主要元素，求中点得到的结果就是错误的。

## 方法二

也可以使用哈希表来统计元素的出现次数，然后遍历哈希表找到数量超出数组半数的元素。

```go
func majorityElement(nums []int) int {
    m := make(map[int]int)
    for _, v := range nums {
        m[v]++
    }
    n := len(nums)
    for k, v := range m {
        if v > n / 2 {
            return k
        }
    }
    return -1
}
```

时间复杂度为**O(n)**。

空间复杂度为**O(n)**。

## 方法三

第三种方法就是本篇的主题，**摩尔投票算法**。

直接看算法名字的话有一些迷惑，但其实很好理解，把每个数字理解为一个参选者，数字出现的次数就是投给这个参选者的票数。

超过 1/2 的数字最多只可能有一个，每次找到两张不同的选票，将这两张选票消除，这个操作有两种情况：

- 两张中有一张是超过 1/2 的选票
- 两张都是少数的选票

消除后超过 1/2 的选票还是超过总数的 1/2。

```go
func majorityElement(nums []int) int {
    //major就是待抵消的数字，count代表major这个数字的数量
    //就是相当于一个栈，入栈的是待抵消的数字，当下一个数字更这个数字相同时，继续入栈（count++），不同时则出栈（count--）
    //当栈为空时，当前遍历到的数字就定义为待抵消的数字，入栈（赋值给major，count++）
    major, count := -1, 0
    for _, v := range nums {
        //没有需要抵消的元素，选中当前值，然后继续遍历
        if count == 0 {
            major = v
            count++
            continue
        }
        //有需要抵消的值，但是和当前遍历到的值相同，增加计数器（代表需要抵消的值的数量+1）
        if major == v {
            count++
            continue
        }
        //有需要抵消的值，且和当前遍历到的值不同，则计数器-1（代表需要抵消的值的数量-1，major的值并不改变，在count减到0之后再遍历到新的值时才会改变）
        count--
    }

    //剩余待消除的值，再遍历一边校验一下这个值的数量是否真的超过半数
    if count > 0 {
        var i int 
        for _, v := range nums {
            if v == major {
                i++
            }
        }
        if i > len(nums) / 2 {
            return major
        }
    }
    return -1
}
```

时间复杂度为**O(n)**。

空间复杂度为**O(1)**。

## 扩展

**考虑超过 1/3 的情况**

找到三个不同的值消除，同样有两种情况：

- 其中有一个是我们要找的那个超过 1/3 选票
- 三个都不是我们要找的

如果抵消的三个值都不是我们要找的，那么显然原本已经占比超过 1/3 的选票，在这轮操作之后将占比更高。



对于第一种情况，我们来简单证明一下。

有一类选票（同一个数值）超过总数的 1/3，我们假设它的数量是 m，总数是 n：
$$
\begin{align*}
& 初始值 \begin{cases} m, （多数选票）\\ n-m,（其余选票） \end{cases}, \frac{n-m}{m}<\frac{\frac{2}{3}}{\frac{1}{3}} \Leftrightarrow \frac{n-m}{m}<2\\
& \Rightarrow \frac{(n-m)-2}{m-1}<2\\
& \Rightarrow (n-m)-2<2(m-1)\\
& \Rightarrow n<3m\\
& \Rightarrow m>\frac{n}{3}\\
\end{align*}
$$
所以对于这种情况，只需要维护 2 个计数器和对应的值来抵消选票：

```go
func majorityElement(nums []int) int {
	one, oneCount, two, twoCount := -1, 0, -1, 0
	for _, v := range nums {
		if oneCount == 0 {
			one = v
			oneCount++
			continue
		}
		if twoCount == 0 {
			two = v
			twoCount++
			continue
		}
		if one == v {
			oneCount++
			continue
		}
		if two == v {
			twoCount++
			continue
		}
		oneCount--
		twoCount--
	}

	if oneCount > 0 && check(one, nums) {
		return one
	}
	if twoCount > 0 && check(two, nums) {
		return two
	}
	return -1
}
func check(i int, nums []int) bool {
	var sum int
	for _, v := range nums {
		if v == i {
			sum++
		}
	}
	if 3 * sum > len(nums)  {
		return true
	}
	return false
}
```



**再推广到 1/k 的情况**

也是同样的道理，给出证明：
$$
\begin{align*}
& 初始值 \begin{cases} m, （多数选票）\\ n-m,（其余选票） \end{cases}, \frac{n-m}{m}<\frac{1 - \frac{1}{k}}{\frac{1}{k}} \Leftrightarrow \frac{n-m}{m}<k-1\\
& \Rightarrow \frac{(n-m)-(k-1)}{m-1}<k-1\\
& \Rightarrow (n-m)-(k-1)<(m-1)(k-1)\\
& \Rightarrow n-m<m(k-1)\\
& \Rightarrow n<km\\
& \Rightarrow m>\frac{n}{k}\\
\end{align*}
$$
这时只需要维护 k-1 个计数器和对应的值来遍历数组就可以了。
