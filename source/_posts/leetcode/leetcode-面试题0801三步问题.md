---
title: leetcode-面试题08.01三步问题
date: 2020-09-15 18:25:45
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-面试题 08.01. 三步问题](https://leetcode-cn.com/problems/three-steps-problem-lcci/)

```java
class Solution {
    public int waysToStep(int n) {

        if (n < 3) {
            return n;
        }
        if (n == 3) {
            return 4;
        }

        //定义三个状态
        long[] dp = new long[n + 1];

        //初始化dp
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 4;

        for (int i = 4; i <= n; i++) {
            dp[i] = (dp[i - 1] + dp[i - 2] + dp[i - 3])%(1000000007);
        }
        return (int)dp[n];
    }
}
```

