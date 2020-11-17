---
title: leetcode-410. 分割数组的最大值
date: 2020-10-21 17:47:56
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)

**题解思路**

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int n = nums.length;
        // 将前i个数分为m断
        int[][] dp = new int[n + 1][m + 1];
        int[] s = new int[n + 1];

        for (int i = 0; i <= n; i++) {
            Arrays.fill(dp[i], Integer.MAX_VALUE);
        }

        dp[0][0] = 0;

        //前缀和
        for (int i = 1; i <= n; i++) {
            s[i] = s[i - 1] + nums[i - 1];
        }

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= Math.min(i, m); j++) {
                for (int k = 0; k < i; k++) {
                    //前k个数分为j-1段,k+1到i分为一段
                    dp[i][j] = Math.min(dp[i][j], Math.max(dp[k][j - 1], s[i] - s[k]));
                }
            }
        }

        return dp[n][m];
    }
}
```

