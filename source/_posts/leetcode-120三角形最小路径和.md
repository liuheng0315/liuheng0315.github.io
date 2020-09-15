---
title: leetcode-120三角形最小路径和
date: 2020-09-11 17:08:18
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-120. 三角形最小路径和](https://leetcode-cn.com/problems/triangle/)

**原始思路**

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        // 初始记录的状态的数组
        int[][] dp = new int[n+1][n+1];

        // 自底向上递推
        for (int i = n - 1; i >=0 ; i--) {
            //从左到右
            for (int j = 0; j <= i; j++) {
                // 定义状态转移方程
                dp[i][j] = Math.min(dp[i+1][j], dp[i+1][j+1]) + triangle.get(i).get(j);
            }
        }
        return dp[0][0];
    }
}
```



**题解思路-状态压缩**

```java
class Solution {
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        // 初始记录的状态的数组
        int[] dp = new int[n+1];

        // 自底向上递推
        for (int i = n - 1; i >=0 ; i--) {
            //从左到右
            for (int j = 0; j <= i; j++) {
                // 定义状态转移方程
                dp[j] = Math.min(dp[j], dp[j+1]) + triangle.get(i).get(j);
            }
        }
        return dp[0];
    }
}
```



