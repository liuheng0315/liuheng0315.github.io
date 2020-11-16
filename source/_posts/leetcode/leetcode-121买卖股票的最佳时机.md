---
title: leetcode-121.买卖股票的最佳时机
date: 2020-09-14 20:41:41
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

**原始思路**

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length < 2){
            return 0;
        }
        int n = prices.length;
        //定义dp数组
        int[][] dp = new int[n][2];
        //初始化数组
        dp[0][0] = 0;
        dp[0][1] = -prices[0];

        for (int i = 1; i < prices.length; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            //此处-prices[i]表示在当前持入股票,因为题目只能允许股票买卖一次
            dp[i][1] = Math.max(dp[i - 1][1], -prices[i]);
        }

        return dp[n-1][0];
    }
}
```



**题解思路--状态压缩**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if(n < 2){
            return 0;
        }
        
        // 定义股票持有及不持有两种状态
        int[] dp = new int[2];
        // 股票不持有
        dp[0] = 0;
        // 股票持有
        dp[1] = -prices[0];
        
        for(int i = 1; i < n; i++){
            dp[0] = Math.max(dp[0], dp[1] + prices[i]);
            dp[1] = Math.max(dp[1], -prices[i]);
        }
        return dp[0];
    }
}
```

