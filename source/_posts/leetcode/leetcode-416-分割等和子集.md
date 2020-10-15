---
title: leetcode-416. 分割等和子集
date: 2020-10-11 23:55:09
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: .
---

#### [leetcode-416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

0-1背包问题

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int n = nums.length;
        int total = 0;
        for(int i = 0; i < nums.length; i++){
            total += nums[i];
        }

        if(total % 2 == 1){
            //总和为奇数
            return false;
        }

        int half = total / 2;

        int[] dp = new int[half+1];
        for(int i = 1; i <= n; i++){
            for(int j = half; j >= nums[i-1]; j--){
                dp[j] = Math.max(dp[j], dp[j-nums[i-1]] + nums[i-1]);
            }
        }
        return dp[half] == half ? true : false;
    }
}
```

