---
title: leetcode-198. 打家劫舍
date: 2020-10-12 23:40:49
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

**原始思路**

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n+1][2];

        for(int i = 1; i <= n; i++){
            dp[i][0] = Math.max(dp[i-1][1], dp[i-1][0]);
            dp[i][1] = dp[i-1][0] + nums[i-1];
        }

        return Math.max(dp[n][0],dp[n][1]);
    }
}
```

