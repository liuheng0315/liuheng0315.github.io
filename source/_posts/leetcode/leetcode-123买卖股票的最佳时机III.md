---
title: leetcode-123.买卖股票的最佳时机III
date: 2020-09-15 00:47:14
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        if (n < 2) {
            return 0;
        }

        //定义一个数组,n表示第几天,3表示交易了几次,2表示持有或者不持有股票
        int[][][] dp = new int[n][3][2];

        //一共有5中状态
        // 表示一直未买进股票
        dp[0][0][0] = 0;
        //表示第一次买进股票,未进行卖出的交易,卖出只能有两次
        dp[0][0][1] = -prices[0];

        //第一次卖出,此状态还未发生,设置为不可能的值
        dp[0][1][0] = -1000000000;
        //在第一次卖出后，然后再进行买入
        dp[0][1][1] = -1000000000;

        //第二次将股票卖出
        dp[0][2][0] = -1000000000;

        for (int i = 1; i < n; i++) {
            dp[i][0][0] = dp[i-1][0][0];
            dp[i][0][1] = Math.max(dp[i - 1][0][1], dp[i - 1][0][0] - prices[i]);
            dp[i][1][0] = Math.max(dp[i - 1][1][0], dp[i - 1][0][1] + prices[i]);
            dp[i][1][1] = Math.max(dp[i - 1][1][1], dp[i - 1][1][0] - prices[i]);
            dp[i][2][0] = Math.max(dp[i - 1][2][0], dp[i - 1][1][1] + prices[i]);
        }

        return Math.max(dp[n - 1][2][0], Math.max(dp[n - 1][1][0], dp[n - 1][0][0]));
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
        // 定义dp数组,此处使用滚动变量
        int buy1 = -prices[0];
        int sell1 = 0;
        int buy2 = -1000000000;
        int sell2 = -1000000000;
        
        for(int i = 1; i < n; i++){
            buy1 = Math.max(buy1, -prices[i]);
            sell1 = Math.max(sell1, buy1 + prices[i]);
            buy2 = Math.max(buy2, sell1 - prices[i]);
            sell2 = Math.max(sell2, buy2 + prices[i]);
        }
        return sell2;
    }
}
```

