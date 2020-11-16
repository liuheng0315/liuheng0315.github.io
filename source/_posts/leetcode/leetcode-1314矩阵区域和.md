---
title: leetcode-1314.矩阵区域和
date: 2020-09-15 17:06:38
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1314. 矩阵区域和](https://leetcode-cn.com/problems/matrix-block-sum/)

```java
class Solution {
    public int[][] matrixBlockSum(int[][] mat, int K) {
        // 确定矩阵的大小
        int n = mat.length;
        int c = mat[0].length;
        int[][] result = new int[n][c];

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < c; j++) {
                //计算原始数组中需要计算的角标
                int xmin = i - K < 0 ? 0 : i - K;
                int xmax = i + K > n - 1 ? n - 1 : i + K;
                int ymin = j - K < 0 ? 0 : j - K;
                int ymax = j + K > c - 1 ? c - 1 : j + K;
                result[i][j] = caculate(mat, xmin, xmax, ymin, ymax);
            }
        }
        return result;
    }

    //计算大小
    public int caculate(int[][] mat, int xmin, int xmax, int ymin, int ymax) {
        int result = 0;
        for (int i = xmin; i <= xmax; i++) {
            for (int j = ymin; j <= ymax; j++) {
                result += mat[i][j];
            }
        }
        return result;
    }
}
```

