---
title: leetcode-213. 打家劫舍 II
date: 2020-10-13 00:15:13
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: .
---

#### [leetcode-213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/)

**原始思路**

```java
class Solution {
    public int rob(int[] nums) {
        int n = nums.length;
        int[][][] dp = new int[n+1][2][2];
        dp[1][0][0] = 0;
        dp[1][1][1] = nums[0];
        for(int i = 2; i <= n; i++){
            //第一家房子没有被抢
            if(i == 2){
                dp[i][0][0] = dp[i-1][0][0];
            }else{
                dp[i][0][0] = Math.max(dp[i-1][0][0],dp[i-1][1][0]);
            }
            dp[i][1][0] = dp[i-1][0][0] + nums[i-1];

            // 第一件房子被抢了
            if(i == n){
                dp[i][0][1] = Math.max(dp[i-1][0][1],dp[i-1][1][1]);
            }else{
                dp[i][0][1] = Math.max(dp[i-1][0][1],dp[i-1][1][1]);
                dp[i][1][1] = dp[i-1][0][1] + nums[i-1];
            }
        }

        int result1 = Math.max(dp[n][0][0], dp[n][1][0]);
        int result2 = Math.max(dp[n][0][1], dp[n][1][1]);
        return Math.max(result1, result2);
    }
}
```

