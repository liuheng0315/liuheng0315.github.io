---
title: leetcode-376. 摆动序列
date: 2020-11-17 10:01:26
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-376. 摆动序列](https://leetcode-cn.com/problems/wiggle-subsequence/)

```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n+1][2];
        if(n==0){
            return 0;
        }
        // 表示前面是下降
        dp[1][0] = 1;
        // 表示前面是上升
        dp[1][1] = 1;

        for(int i = 2; i <=n; i++){
            if(nums[i-1]>nums[i-2]){
                // 上升
                dp[i][1] = dp[i-1][0] + 1;
                dp[i][0] = dp[i-1][0];
            }else if(nums[i-1]<nums[i-2]){
                // 下降
                dp[i][0] = dp[i-1][1] + 1;
                dp[i][1] = dp[i-1][1];
            }else{
                dp[i][0] = dp[i-1][0];
                dp[i][1] = dp[i-1][1];
            }
        }
        return Math.max(dp[n][0],dp[n][1]);
    }
}
```

