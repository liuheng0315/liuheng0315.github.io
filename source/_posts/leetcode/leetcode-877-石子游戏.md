---
title: leetcode-877. 石子游戏
date: 2020-10-12 14:44:31
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-877. 石子游戏](https://leetcode-cn.com/problems/stone-game/)

```java
class Solution {
    public boolean stoneGame(int[] piles) {
        int n = piles.length;
        int[][] dp = new int[n][n];
        //初始化dp,当i==j时,证明只有这个数字可选了
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (i == j) {
                    dp[i][j] = piles[i];
                }
            }
        }

        // 斜着遍历距离,每次求先手的与后手的差值
        for (int i = n - 2; i >= 0; i--) {
            //从倒数第二行开始遍历
            for (int j = i + 1; j < n; j++) {
                // 从i+1列开始遍历
                dp[i][j] = Math.max(piles[i] - dp[i + 1][j], piles[j] - dp[i][j - 1]);
            }
        }
        return dp[0][n-1] > 0 ? true : false;
    }
}
```

