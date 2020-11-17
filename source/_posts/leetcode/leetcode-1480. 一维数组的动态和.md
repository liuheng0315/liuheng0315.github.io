---
title: '1480. 一维数组的动态和'
date: 2020-10-14 17:22:13
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1480. 一维数组的动态和](https://leetcode-cn.com/problems/running-sum-of-1d-array/)

```java
class Solution {
    public int[] runningSum(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        dp[0] = nums[0];
        for (int i = 1; i < n; i++) {
            dp[i] = dp[i - 1] + nums[i];
        }
        return dp;
    }
}
```

