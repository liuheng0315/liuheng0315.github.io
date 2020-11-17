---
title: leetcode-978. 最长湍流子数组
date: 2020-10-13 14:16:49
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-978. 最长湍流子数组](https://leetcode-cn.com/problems/longest-turbulent-subarray/)

**题解思路**

```java
class Solution {
    public int maxTurbulenceSize(int[] A) {
        int n = A.length;
        int[][] dp = new int[n+1][2];
        //初始化dp,第二位的0代表前一是下降,1代表为上升
        dp[1][0] = 1;
        dp[1][1] = 1;

        for (int i = 2; i <= n; i++) {
            if (A[i - 2] < A[i - 1]) {
                // 当前为上升,所有前一个应该为下降
                dp[i][1] = dp[i - 1][0] + 1;

                // 此时设置下降为1
                dp[i][0] = 1;
            }else if(A[i - 2] > A[i - 1]){
                // 如果当前为下降,那么前一个应该为上升
                dp[i][0] = dp[i - 1][1] + 1;

                //那么当前上升的为1
                dp[i][1] = 1;
            }else {
                //相等时,都为1
                dp[i][0] = 1;
                dp[i][1] = 1;
            }
        }

        int ans = Integer.MIN_VALUE;
        for (int i = 1; i <= n; i++) {
            ans = Math.max(ans, Math.max(dp[i][0], dp[i][1]));
        }

        return ans;
    }
}
```

