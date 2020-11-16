---
title: leetcode-1130. 叶值的最小代价生成树
date: 2020-10-23 14:06:41
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1130. 叶值的最小代价生成树](https://leetcode-cn.com/problems/minimum-cost-tree-from-leaf-values/)

**区间dp**

```java
class Solution {
    public int mctFromLeafValues(int[] arr) {
        int n = arr.length;
        int[][] dp = new int[n + 1][n + 1];
        int[][] max = new int[n + 1][n + 1];
        for (int i = 1; i <= n; i++) {
            int maxValue = arr[i - 1];
            for (int j = i; j <= n; j++) {
                maxValue = Math.max(maxValue, arr[j - 1]);
                max[i][j] = maxValue;

                //初始化dp
                if (i != j) {
                    dp[i][j] = Integer.MAX_VALUE;
                }
            }
        }

        for (int l = 1; l < n; l++) {
            for (int i = 1; i + l <= n; i++) {
                int j = i + l;
                for (int k = i; k < j; k++) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k + 1][j] + max[i][k] * max[k + 1][j]);
                }
            }
        }
        return dp[1][n];
    }
}
```

