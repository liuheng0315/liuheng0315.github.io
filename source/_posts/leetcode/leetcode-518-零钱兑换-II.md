---
title: leetcode-518. 零钱兑换 II
date: 2020-09-17 13:42:38
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-518. 零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)

**题解思路**

```java
class Solution {
    public int change(int amount, int[] coins) {
        // 该题有两种状态 使用前i个硬币，第二个是硬币的总额
        // 定义dp,dp表示组合数
        int[][] dp = new int[coins.length + 1][amount + 1];

        //使用0个硬币组合0，是一种组合
        for (int i = 0; i <= coins.length ; i++) {
            dp[i][0] = 1;
        }

        //硬币首先看是否能装的下, 装的下的情况又分为可选可不选
        for (int i = 1; i <= coins.length; i++) {
            for (int j = 1; j <= amount ; j++) {
                if (j - coins[i-1] >= 0) {
                    //可以选择这个硬币,也可以不选择这个硬币
                    dp[i][j] = dp[i-1][j] + dp[i][j - coins[i - 1]];
                }else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[coins.length][amount];
    }
}
```

