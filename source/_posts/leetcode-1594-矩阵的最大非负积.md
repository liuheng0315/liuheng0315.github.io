---
title: leetcode-1594. 矩阵的最大非负积
date: 2020-09-21 16:23:06
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1594. 矩阵的最大非负积](https://leetcode-cn.com/problems/maximum-non-negative-product-in-a-matrix/)

```java
class Solution {
    public int maxProductPath(int[][] grid) {
        int n1 = grid.length;
        int n2 = grid[0].length;
        long[][][] dp = new long[n1][n2][2];

        dp[0][0][0] = grid[0][0];
        dp[0][0][1] = grid[0][0];
        //初始化dp,第三维0表示最小值,1表示最大值
        for (int i = 1; i < n2; i++) {
            dp[0][i][0] = dp[0][i - 1][0] * grid[0][i];
            dp[0][i][1] = dp[0][i - 1][0] * grid[0][i];
        }
        for (int i = 1; i < n1; i++) {
            dp[i][0][0] = dp[i - 1][0][0] * grid[i][0];
            dp[i][0][1] = dp[i - 1][0][0] * grid[i][0];
        }

        for (int i = 1; i < n1; i++) {
            for (int j = 1; j < n2; j++) {
                if (grid[i][j] >= 0) {
                    dp[i][j][0] = Math.min(dp[i][j - 1][0], dp[i - 1][j][0]) * grid[i][j];
                    dp[i][j][1] = Math.max(dp[i][j - 1][1], dp[i - 1][j][1]) * grid[i][j];
                } else {
                    dp[i][j][0] = Math.max(dp[i][j - 1][1], dp[i - 1][j][1]) * grid[i][j];
                    dp[i][j][1] = Math.min(dp[i][j - 1][0], dp[i - 1][j][0]) * grid[i][j];
                }
            }
        }

        return dp[n1 - 1][n2 - 1][1] < 0 ? -1 : (int)(dp[n1 - 1][n2 - 1][1] % (1000000000 + 7));
    }
}
```

