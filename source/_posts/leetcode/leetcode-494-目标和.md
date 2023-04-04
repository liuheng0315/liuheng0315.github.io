---
title: leetcode-494. 目标和
date: 2021-07-20 16:21:43
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [494. 目标和](https://leetcode-cn.com/problems/target-sum/)

```java
class Solution {
    public int findTargetSumWays(int[] nums, int target) {
        int n = nums.length;
        int sum = 0;
        for(int num : nums){
            sum += num;
        }

        // neg必须是能被2除的
        if((sum - target)<0 || (sum - target)%2 != 0){
            return 0;
        }

        int neg = (sum - target)/2;
        int[][] dp = new int[n+1][neg+1];
        dp[0][0] = 1;
        for(int i = 1; i <= n; i++){
            for(int j = 0; j <= neg; j++){
                dp[i][j] = dp[i-1][j];
                if(j >= nums[i-1]){
                    dp[i][j] = dp[i-1][j] + dp[i-1][j-nums[i-1]];
                }
            }
        }
        return dp[n][neg];
    }
}
```



