---
title: 363. 矩形区域不超过 K 的最大数值和
date: 2021-04-22 18:27:01
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [363. 矩形区域不超过 K 的最大数值和](https://leetcode-cn.com/problems/max-sum-of-rectangle-no-larger-than-k/)

```java
class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        int row = matrix.length;
        int columns = matrix[0].length;
        int[][] dp = new int[row+1][columns+1];
        // 初始化dp
        dp[1][1] = matrix[0][0];
        for (int i = 1; i <= row; i++) {
            for (int j = 1; j <= columns; j++) {
                int ans = 0;
                for (int l = 1; l <= i; l++) {
                    ans += matrix[l-1][j-1];
                }
                dp[i][j] = dp[i][j-1] + ans;
            }
        }
        
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < columns ; j++) {
                for (int i2 = i + 1; i2 <= row; i2++) {
                    for (int j2 = j + 1; j2 <= columns; j2++) {
                        int val = dp[i2][j2] - dp[i][j2] - dp[i2][j] + dp[i][j];
                        if (val <= k) {
                            res = Math.max(res, val);
                        }
                    }
                }
            }
        }
        return res;
    }
}
```

