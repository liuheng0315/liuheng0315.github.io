---
title: leetcode-1582.二进制矩阵中的特殊位置
date: 2020-09-15 13:01:16
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1582. 二进制矩阵中的特殊位置](https://leetcode-cn.com/problems/special-positions-in-a-binary-matrix/)

**原始思路**

```java
class Solution {
    int count = 0;
    public int numSpecial(int[][] mat) {
        int row = mat.length;
        int cols = mat[0].length;

        for (int i = 0; i < row; i++) {
            for (int j = 0; j < cols; j++) {
                if (mat[i][j] == 1) {
                    dfs(mat, i, j, row, cols);
                }
            }
        }
        return count;
    }

    public void dfs(int[][] mat, int i, int j, int row, int cols) {
        boolean rowBool = true;
        for (int k = 0; k < row; k++) {
            if (k == i) {
                continue;
            }
            if (mat[k][j] == 1){
                rowBool = false;
                break;
            }
        }

        boolean colBool = true;
        for (int k = 0; k < cols; k++) {
            if (k == j) {
                continue;
            }
            if (mat[i][k] == 1){
                colBool = false;
                break;
            }
        }

        if (rowBool && colBool) {
            count++;
        }
    }
}

```

