---
title: leetcode-剑指 Offer 47. 礼物的最大价值
date: 2020-10-12 17:25:28
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-剑指 Offer 47. 礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

```java
class Solution {
    public int maxValue(int[][] grid) {
        int n1 = grid.length;
        int n2 = grid[0].length;
        int[][] dp = new int[n1][n2];
        // 初始化dp
        dp[0][0] = grid[0][0];
        for (int i = 1; i < n1; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for (int i = 1; i < n2; i++) {
            dp[0][i] = dp[0][i - 1] + grid[0][i];
        }

        for (int i = 1; i < n1; i++) {
            for (int j = 1; j < n2; j++) {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }

        return dp[n1 - 1][n2 - 1];
    }
}
```

