---
title: leetcode-1277. 统计全为 1 的正方形子矩阵
date: 2020-10-14 12:51:36
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1277. 统计全为 1 的正方形子矩阵](https://leetcode-cn.com/problems/count-square-submatrices-with-all-ones/)

**原始思路**

```java
class Solution {
    public int countSquares(int[][] matrix) {
        int n1 = matrix.length;
        int n2 = matrix[0].length;

        int[][] dp = new int[n1][n2];

        // 初始化dp
        dp[0][0] = matrix[0][0];
        for (int i = 1; i < n1; i++) {
            dp[i][0] = dp[i-1][0] + matrix[i][0];
        }
        for (int i = 1; i < n2; i++) {
            dp[0][i] = dp[0][i-1] + matrix[0][i];
        }

        for (int i = 1; i < n1; i++) {
            for (int j = 1; j < n2; j++) {
                if (matrix[i][j] == 0) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1] - dp[i-1][j-1];
                }else {
                    // 加入i,j 这个节点后能构成多少个矩形
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1] - dp[i-1][j-1] + judge(matrix, i, j);
                }

            }
        }

        return dp[n1 - 1][n2 - 1];
    }

    public int judge(int[][] matrix, int x, int y) {
        int count = 0;
        //最多能构成的矩形的长度
        int len = Math.min(x, y);
        for (int i = 0; i <= len; i++) {
            // 判断x-1， y-i 到y
            boolean b = true;
            for (int j = y-i; j <= y; j++) {
                if (matrix[x - i][j] == 0) {
                    b = false;
                    break;
                }
            }

            if (!b) {
                return count;
            }

            for (int j = x - i; j <= x; j++) {
                if (matrix[j][y-i] == 0) {
                    b = false;
                    break;
                }
            }

            if (!b) {
                return count;
            }

            if (b) {
                count ++;
            }
        }
        return count;
    }
}
```

