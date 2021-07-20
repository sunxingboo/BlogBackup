---
title: 几道01背包的应用题
permalink: 01pack-application
date: 2021-07-15
tags: [数据结构与算法,动态规划,背包]
---

学完 $01$ 背包之后，简单记录一下几道 $01$ 背包应用题的解题思路和代码实现（有的题目在不考虑超时的情况下，也可以通过回溯等其他解法求解，这里暂不考虑，只记录利用动态规划 $01$ 背包的解题思路）。

通用函数 $max$ 代码如下，后续不再记录：

```go
func max(i, j int) int {
    if i > j {
		    return i
	  }
	  return j
}
```

<!--more-->

## 分割等和子集

[leetcode 416](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

### 问题描述

给你一个只包含*正整数*的*非空*数组 $nums$ 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

示例：

```
输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
```

### 问题分析

本题的预期是将一个数组分为两个子数组，且这两个子数组的元素和相等。

假设数组元素和为 $sum$，那么显然只有在 $sum$ 为**偶数**时才可能成立，若 $sum$ 为奇数则数组必然不可拆分。

拆分后的子数组元素和为必须为 $\frac{sum}{2}$，否则是不成立的。

那么实际上现在就可以将这个问题转化为 $01$ 背包问题了。将 $\frac{sum}{2}$ 理解为**背包**的容量，物品以及物品的**重量**和**价值**都是 $nums$。

这样就把问题转化成了：一个容量为 $\frac{sum}{2}$ 的背包，任意从物品 $nums$ 中选择，最大能容纳的物品重量。当能容纳的最大重量等于 $\frac{sum}{2}$ （刚好装满）时，则说明数组 $nums$ 能拆分成两个元素和相等的子数组。

### golang代码实现

```go
func canPartition(nums []int) bool {
  	//先判断数组和是否为偶数
    var sum int
	  for _, v := range nums {
        sum += v
    }
    if sum % 2 != 0 {
        return false
    }

  	//取数组和的一半作为背包容量
	  target := sum / 2
	  dp := make([]int, target + 1)
	  for i := 0; i < len(nums); i++ {
		    for j := target; j >= nums[i]; j-- {
			      if j >= nums[i] {
				        dp[j] = max(dp[j], dp[j - nums[i]] + nums[i])
			      }
		    }
	  }

  	//当背包刚好装满时，数组可以拆分为元素和相等的两个子数组
    return dp[target] == target
}
```

时间复杂度 $O(mn)$，$m$ 是数组元素总数，$n$ 是背包最大容量。

空间复杂度 $O(n)$。

## 最后一块石头的重量

[leetcode 1049](https://leetcode-cn.com/problems/last-stone-weight-ii/)

### 问题描述

有一堆石头，用整数数组 $stones$ 表示。其中 $stones[i]$ 表示第 $i$ 块石头的重量。

每一回合，从中选出任意两块石头，然后将它们一起粉碎。假设石头的重量分别为 $x$ 和 $y$，且 $x \leq y$。那么粉碎的可能结果如下：

- 如果 $x = y$，那么两块石头都会被完全粉碎；

- 如果 $x \neq y$，那么重量为 $x$ 的石头将会完全粉碎，而重量为 $y$ 的石头新重量为 $y-x$。

最后，最多只会剩下一块 石头。返回此石头 最小的可能重量 。如果没有石头剩下，就返回 $0$。

示例

```
输入：stones = [2,7,4,1,8,1]
输出：1
解释：
组合 2 和 4，得到 2，所以数组转化为 [2,7,1,8,1]，
组合 7 和 8，得到 1，所以数组转化为 [2,1,1,1]，
组合 2 和 1，得到 1，所以数组转化为 [1,1,1]，
组合 1 和 1，得到 0，所以数组转化为 [1]，这就是最优值。
```

### 问题分析

题目期望的是，在两两碰撞之后，最终剩下的石头最小。

那么实际可以理解为，将数组**尽量的**分为元素和相等的两个子数组，这样两个子数组中的石头两两相撞，重量大的在相撞抵消掉一部分重量之后放回原数组，再继续选择两个石头相撞，那么最终剩余的重量就是最小的。

如果拆分出的两个子数组元素和恰好相等，则最终剩余的石头重量就是 $0$。

这样实际上又把问题转化为了 $01$ 背包问题：

假设石头数组的重量和为 $sum$，现在有一个背包的最大容量为 $\frac{sum}{2}$，当背包装满时剩余的石头重量为 $sum - \frac{sum}{2}$。当 $sum$ 为奇数时，$\frac{sum}{2}$ 是向下取整的，所以整体来说 $sum - \frac{sum}{2} \geq \frac{sum}{2}$。

所以只考虑背包最多能容纳多少重量的石头就可以了，假设最多容纳石头重量为 $dp[\frac{sum}{2}]$，则剩余石头为 $sum - dp[\frac{sum}{2}]$，则碰撞抵消完成后最终剩余的就是两者的差值  $(sum - dp[\frac{sum}{2}]) - dp[\frac{sum}{2}]$ （因为 $(sum - dp[\frac{sum}{2}]) \geq dp[\frac{sum}{2}]$）。

与上一题相似，物品的重量和价值都可以理解为石头数组 $stones$。

### golang代码实现

```go
func lastStoneWeightII(stones []int) int {
 		//求和，这道题奇数和偶数都是可以的
    var sum int
    for _, v := range stones {
        sum += v
    } 
  
    //求背包最大重量（将石头总和拆分，尽可能平均分配到两部分中，这样才能最大程度的消除）
    target := sum / 2
    dp := make([]int, target + 1)
    for i := 0; i < len(stones); i++ {
        for j := target; j >= stones[i]; j-- {
            dp[j] = max(dp[j], dp[j - stones[i]] + stones[i])
        }
    }
  	
    //剩余部分重量减去背包容纳的最大重量就是抵消后剩余的最小值
    return (sum - dp[target]) - dp[target]
}
```

时间复杂度 $O(mn)$，$m$ 是石头总数，$n$ 是背包最大容量。

空间复杂度 $O(n)$。

## 目标和

[leetcode 494](https://leetcode-cn.com/problems/target-sum/)

### 问题描述

给你一个整数数组 nums 和一个整数 target 。

向数组中的每个整数前添加 '+' 或 '-' ，然后串联起所有整数，可以构造一个 表达式 ：

例如，nums = [2, 1] ，可以在 2 之前添加 '+' ，在 1 之前添加 '-' ，然后串联起来得到表达式 "+2-1" 。

返回可以通过上述方法构造的、运算结果等于 target 的不同表达式的数目。

```
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

### 问题分析

**思路**

前两道题目可以很容易的转换成 $01$ 背包问题求解，但这道题直观上看就不那么明显了。

题目的目的是，给一组数，可加可减，最终得到需要的目标值，求一共有多少种不同的组合方式。

假设元素总和为 $sum$，最终需要的目标值是 $target$，选取过程中用于执行加法的元素和为 $x$，用于减法的元素和为 $y$，那么则有：
$$
\begin{align*}
&\begin{cases}
x - y = target\\
x + y = sum\\
\end{cases}\\
&\Rightarrow 2x = target + sum\\
&\Rightarrow x = \frac{target + sum}{2}
\end{align*}
$$
也就是说，只要从大集合中，找到一组元素和为 $\frac{target + sum}{2}$ 的子集合，就找到了一种得到 $target$ 的方式。那么题目的答案就可以转化为求不同的和为 $\frac{target + sum}{2}$ 的子集数量。

也就是可以理解为，一个容量为 $\frac{target + sum}{2}$ 的背包，从 $nums$ 中任意选择物品，最多有多少种不同的方式能装满背包。物品的重量和价值都是 $nums[i]$。

这样就把这个问题转化成了 $01$ 背包问题。



**$dp$ 数组定义**

前面两道题目的 $dp$ 数组与 $01$ 背包基础定义的 $dp$ 数组基本相同，但目前这道题目显然 $dp$ 数组的含义就不相同了。

前面两题 $dp$ 数组下标定义 $dp[j]$ 代表的是容量为 $j$ 的背包，从物品 $0$ ~ $i$ 中任意选择能容纳的最大物品价值和。

而本题 $dp[j]$ 代表把容量为 $j$ 的背包装满有多少种不同的方式（与爬楼梯的问题很相似）。



**递推公式**
$$
dp[j] = dp[j] + dp[j - nums[i]]
$$
装满最大容量为 $j - nums[i]$ 的背包共有 $dp[j - nums[i]]$ 种方法。那么要装满最大容量为 $dp[j]$ 的背包，只需要再选取 $nums[i]$ 就可以了，所以与装满 $dp[j - nums[i]]$ 的方法种类是相等的。 

那么对于每一个元素 $nums[i]$ 在 $dp[j]$ 位置，都能通过 $dp[j - nums[i]]$ 定位到选择 $nums[i]$ 来使背包能够装满的方法数量，索引对每一次遍历求和，就得到了填满容量为 $j$ 的背包的方法总数 $dp[j]$。

*这里直接使用了一维 $dp$ 数组，不如使用二维 $dp$ 数组容易理解。*



**$dp$ 数组初始化**

除了 $dp$ 数组的定义和递推公式与前两道题不同之外，另一个不同点就是 $dp$ 数组的初始化方式。

之前的初始化，在背包最大容量为 $0$ 时，将对应的 $dp$ 数组值设置为了 $0$，含义是**容量为0的背包**，能装入的物品最大价值为 $0$ （这是与 $dp$ 数组定义有关的）。

而在本题中 $dp$ 数组每一个下标值代表的是**装满**该下标值容量的背包的方法总数，那么对于 $dp[0]$ （也就是容量为 $0$ 的背包），装满它的方法总数就不应该是 $0$， 而应该是 $1$，因为**一个物品都不选择**也是一种选择方法。

所以本题的 $dp$ 数组初始化方式应该是：
$$
dp[0] = 1
$$
**举例推导**

以题目示例为例，推演一下 $dp$ 数组变化过程帮助理解：
$$
\begin{align}
bagWeight &= \frac{sum + target}{2}\\
					&= \frac{5 + 3}{2}\\
					&= 4
\end{align}
$$
所以背包初始化后为：
$$
\begin{array}{|c|c|c|c|c|}
  \hline 1&0&0&0&0\\
  \hline
\end{array}\\填满容量为 \ 0 \ 的背包只有一种方法，就是什么都不选
$$
只从第 $1$ 个物品中选择：
$$
nums[0] \qquad \begin{array}{|c|c|c|c|c|}
  \hline 1&1&0&0&0\\
  \hline
\end{array}\\ 选择\ nums[0]\ 可以填满容量为\ 1 \ 的背包
$$


从前 $2$ 个物品中选择：
$$
nums[1] \qquad \begin{array}{|c|c|c|c|c|}
  \hline 1&2&1&0&0\\
  \hline
\end{array} \\ 选择\ nums[0]\ 或 \ nums[1] \ 都可以填满容量为\ 1\ 的背包，有\ 2\ 种方法\\
同时选择\ nums[0]\ 和 \ nums[1] \ 填满容量为\ 2\ 的背包，只有这\ 1\ 种方法
$$
*以下选择情况同理，不再赘述，就是选择的元素进行组合。*

从前 $3$ 个物品中选择：
$$
nums[2] \qquad \begin{array}{|c|c|c|c|c|}
  \hline 1&3&3&1&0\\
  \hline
\end{array}
$$
从前 $4$ 个物品中选择：
$$
nums[3] \qquad \begin{array}{|c|c|c|c|c|}
  \hline 1&4&6&4&1\\
  \hline
\end{array}
$$
从前 $5$ 个物品中选择：
$$
nums[4] \qquad \begin{array}{|c|c|c|c|c|}
  \hline 1&5&10&10&5\\
  \hline
\end{array}
$$

### golang代码实现

```go
func findTargetSumWays(nums []int, target int) int {
    //求和
    var sum int
    for _, v := range nums {
        sum += v
    }
    //1. 目标值比元素和还大，无解
    //2. 对 2 不能整除，无解。
    //因为进行的是整数加减法，不能整除的话就会向下取整，那么背包容量就会偏小于我们预期的容量，比如本示例的 target 如果等于 2，那么预期的背包重量就需要是 3.5，但进行的是整数加减法，不可能得到 3.5
    if target > sum || (target + sum) % 2 != 0 {
        return 0
    }
    //计算背包最大重量，防止溢出 (sum + target)/2 可能溢出
    bag := sum - (sum - target) / 2
    dp := make([]int, bag + 1)
    dp[0] = 1
    for i := 0; i < len(nums); i++ {
        for j := bag; j >= nums[i]; j-- {
            dp[j] += dp[j - nums[i]]
        }
    }
    fmt.Println(dp)
    return dp[bag]
}
```

## 一和零

[leetcode 474](https://leetcode-cn.com/problems/ones-and-zeroes/)

### 问题描述

给你一个二进制字符串数组 $strs$ 和两个整数 $m$ 和 $n$ 。

请你找出并返回 $strs$ 的最大子集的大小，该子集中最多 有 $m$ 个 $0$ 和 $n$ 个 $1$ 。

如果 $x$ 的所有元素也是 $y$ 的元素，集合 $x$ 是集合 $y$ 的子集 。

示例：

```
输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3
输出：4
解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。
其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。
```

### 问题分析



### golang代码实现

```go
func findMaxForm(strs []string, m, n int) int {
    dp := make([][]int, m + 1)
	  for i, _ := range dp {
		    dp[i] = make([]int, n + 1)
	  }
    //遍历物品
	  for _, str := range strs { 
		    var zeroNum, oneNum int
		    for i := 0; i < len(str); i++ {
			      if str[i] == '0' {
				        zeroNum++
			      } else {
				        oneNum++
			      }
		    }

		    for i := m; i >= zeroNum; i-- {
			      for j := n; j >= oneNum; j-- {
				        dp[i][j] = max(dp[i][j], dp[i-zeroNum][j-oneNum]+1)
			      }
		    }
	  }
	  return dp[m][n]
}
```

