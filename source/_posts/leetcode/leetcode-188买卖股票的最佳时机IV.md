---
title: leetcode-188.买卖股票的最佳时机IV
date: 2020-09-15 12:29:28
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

**题解思路**

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int n = prices.length;
        if (n <= 0 || k <= 0) {
            return 0;
        }

        if (2*k > n) {
            int res = 0;
            for (int i = 1; i < n; i++) {
                res += Math.max(0, prices[i] - prices[i - 1]);
            }
            return res;
        }

        // 分别表示第几天,第几次售卖,
        int[][][] dp = new int[n][k+1][2];

        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= k; j++) {
                dp[i][j][0] = -1000000000;
                dp[i][j][1] = -1000000000;
            }
        }

        dp[0][0][0] = 0;
        dp[0][1][1] = -prices[0];

        for (int i = 1; i < n; i++) {
            for (int j = 0; j <=k ; j++) {
                dp[i][j][0] = Math.max(dp[i - 1][j][0], dp[i - 1][j][1] + prices[i]);

                //只要要交易一次
                if (j > 0) {
                    dp[i][j][1] = Math.max(dp[i - 1][j][1], dp[i - 1][j - 1][0] - prices[i]);
                }
            }
        }

        int res = 0;
        for (int j = 0; j <= k; j++) {
            res = Math.max(res, dp[n-1][j][0]);
        }

        return res;
    }
}

```

