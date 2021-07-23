---
Title: 几道完全背包问题
Premalink: complete-pack-application
Date: 2021-07-23
Tags: [数据结构与算法,动态规划,背包]
---

## 零钱兑换

#### [leetcode 518](https://leetcode-cn.com/problems/coin-change-2/)

示例

```
输入：amount = 5, coins = [1, 2, 5]
输出：4
解释：有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

### 问题分析

本笔记不再考虑二维 $dp$ 数组情况，直接使用一维 $dp$ 数组，下面几道题目也一样。

本题中要凑的**总金额**就是背包的最大重量，并且必须要把背包填满。

要求的是能填满背包的方法数（硬币的选择方式组合数）。

**$dp$ 数组定义**

$dp[j]$ 代表为填满容量为 $j$ 背包有 $dp[j]$ 种方式。那么对于每一个物品 $coins[i]$ 



```go
var (
	coins = []int{1, 2, 5}
)

func change(amount int, coins []int) int {
	dp := make([]int, amount + 1)
	dp[0] = 1
	for i := 0; i < len(coins); i++ {
		for j := 1; j <= amount; j++ {
			if j >= coins[i] {
				dp[j] += dp[j - coins[i]]
			}
		}
	}
	return dp[amount]
}
```

