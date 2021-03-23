---
title: leetcode-54. 螺旋矩阵
date: 2021-03-19 15:47:21
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [54. 螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> ans = new ArrayList<>();
        int m = matrix.length;
        int n = matrix[0].length;
        // 定义是否被访问过
        boolean[][] visit = new boolean[m][n];
        // 定义四个方向
        int[][] direction = new int[][]{{0,1},{1,0},{0,-1},{-1,0}};
        int total = m*n;
        int index = 0;
        // 定义行和列
        int i = 0;
        int j = 0;
        for(int k = 0; k < total; k++){
            ans.add(matrix[i][j]);
            visit[i][j] = true;
            int nextI = i + direction[index][0];
            int nextJ = j + direction[index][1];
            if(nextI<0||nextI>=m||nextJ<0||nextJ>=n||visit[nextI][nextJ]){
                index = (index + 1)%4;
            }
            i = i + direction[index][0];
            j = j + direction[index][1];
        }
        return ans;
    }
}
```

