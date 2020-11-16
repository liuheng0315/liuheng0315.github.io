---
title: leetcode-1043. 分隔数组以得到最大和
date: 2020-10-12 20:44:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1043. 分隔数组以得到最大和](https://leetcode-cn.com/problems/partition-array-for-maximum-sum/)

**题解思路**

```java
class Solution {
    public int maxSumAfterPartitioning(int[] arr, int k) {
        int n = arr.length;
        int[] dp = new int[n + 1];

        for (int i = 1; i <= n; i++) {
            //前i个dp的和
            int max = Integer.MIN_VALUE;
            for (int j = 1; j <= Math.min(k, i); j++) {
                max = Math.max(max, arr[i - j]);
                dp[i] = Math.max(dp[i], dp[i - j] + j * max);
            }
        }
        return dp[n];
    }
}
```

