---
title: leetcode-718. 最长重复子数组
date: 2020-12-15 09:23:47
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-718. 最长重复子数组](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)

```java
class Solution {
    public int findLength(int[] A, int[] B) {
        int n1 = A.length;
        int n2 = B.length;
        int[][] dp = new int[n1][n2];
        int ans = 0;
        for(int i = 0; i < n1; i++){
            if(B[0] == A[i]){
                dp[i][0] = 1;
            }
        }

        for(int i = 0; i < n2; i++){
            if(A[0] == B[i]){
                dp[0][i] = 1;
            }
        }

        for(int i = 1; i < n1; i++){
            for(int j = 1; j < n2; j++){
                if(A[i] == B[j]){
                    dp[i][j] = dp[i-1][j-1] + 1;
                }
                ans = Math.max(ans, dp[i][j]);
            }
        }
        return ans;
    }
}
```

