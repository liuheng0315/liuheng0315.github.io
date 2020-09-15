---
title: leetcode-309最佳买卖股票时机含冷冻期
date: 2020-09-15 17:26:05
tags:
---

#### [leetcode-309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

**原始思路**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n < 2) {
            return 0;
        }

        //定义dp数组
        int[][][] dp = new int[n][2][2];

        //未处于冷冻期
        dp[0][0][0] = 0;
        dp[0][0][1] = -prices[0];
        //处于冷冻期
        dp[0][1][0] = 0;
        dp[0][1][1] = -prices[0];

        for (int i = 1; i < n; i++) {
            dp[i][1][0] = Math.max(dp[i - 1][1][0], dp[i - 1][0][1] + prices[i]);
            dp[i][0][1] = Math.max(dp[i - 1][0][1], dp[i - 1][0][0] - prices[i]);
            dp[i][0][0] = Math.max(dp[i - 1][0][0], dp[i - 1][1][0]);
            dp[i][1][1] = Math.max(dp[i - 1][1][1], dp[i - 1][1][0] - prices[i]);
        }
        return Math.max(dp[n - 1][0][0], dp[n - 1][1][0]);
    }
}
```



**题解思路**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if(n < 2){
            return 0;
        }
        
        // 定义dp数组
        int[][] dp = new int[n][3];
        
        //初始化dp
        //未持有股票
        dp[0][0] = 0;
        //持有股票
        dp[0][1] = -prices[0];
        //处于冷冻期
        dp[0][2] = 0;
        
        for(int i = 1; i < n; i++){
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
            dp[i][1] = Math.max(dp[i-1][1], dp[i-1][2] - prices[i]);
            dp[i][2] = dp[i-1][0];
        }
        
        return Math.max(dp[n-1][0], dp[n-1][1]);
    }
}
```

