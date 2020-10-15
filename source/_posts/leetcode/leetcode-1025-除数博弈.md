---
title: leetcode-1025. 除数博弈
date: 2020-09-17 14:09:14
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1025. 除数博弈](https://leetcode-cn.com/problems/divisor-game/)

**原始思路**

```java
class Solution {
    public boolean divisorGame(int N) {

        boolean[] dp = new boolean[N + 1];
        if (N == 1) {
            return false;
        }
        if (N == 2) {
            return true;
        }
        if (N == 3) {
            return false;
        }
        dp[1] = false;
        dp[2] = true;
        dp[3] = false;

        for (int i = 4; i <= N; i++) {
            dp[i] = !dp[i - 1];
        }
        return dp[N];
    }
}
```

