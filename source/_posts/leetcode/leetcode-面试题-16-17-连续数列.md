---
title: 面试题16.17.连续数列
date: 2020-09-17 13:36:16
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode面试题 16.17. 连续数列](https://leetcode-cn.com/problems/contiguous-sequence-lcci/)

**原始思路**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int n = nums.length;
        if (n == 0) {
            return 0;
        }
        int[] dp = new int[n + 1];
        for (int i = 1; i <= n; i++) {
            dp[i] = nums[i - 1];
        }

        for (int i = 1; i <= n; i++) {
            dp[i] = Math.max(dp[i], dp[i - 1] + dp[i]);
        }
        int res = Integer.MIN_VALUE;
        for (int i = 1; i <= n; i++) {
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```

