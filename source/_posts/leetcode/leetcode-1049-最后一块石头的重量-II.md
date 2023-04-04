---
title: leetcode-1049. 最后一块石头的重量 II
date: 2021-07-21 19:15:28
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1049. 最后一块石头的重量 II](https://leetcode-cn.com/problems/last-stone-weight-ii/)

```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int n = stones.length;
        // 总重量
        int sum = 0;
        for(int i = 0; i < n; i++){
            sum += stones[i];
        }

        // 想要返回石头的重量最小，则需要能达到的被摧毁的最大，最理想的结果是为0，假设为0是能达到的情况
        int neg = sum/2;
        boolean[][] dp = new boolean[n+1][neg+1];
        dp[0][0] = true;
        for(int i = 1; i <= n; i++){
            for(int j = 0; j <= neg; j++){
                dp[i][j] = dp[i-1][j];
                if(j >= stones[i-1]){
                    dp[i][j] = dp[i-1][j] || dp[i-1][j-stones[i-1]];
                }
            }
        }

        for(int j = neg; j >= 0; j--){
            if(dp[n][j]){
                return sum - 2*j;
            }
        }
        return 0;
    }
}
```

