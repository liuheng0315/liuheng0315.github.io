---
title: leetcode-392. 判断子序列
date: 2020-09-17 13:39:02
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-392. 判断子序列](https://leetcode-cn.com/problems/is-subsequence/)

**题解思路**

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int n1 = s.length();
        int n2 = t.length();

        boolean[][] dp = new boolean[n1 + 1][n2 + 1];

        for (int i = 1; i <= n1; i++) {
            dp[i][0] = false;
        }

        for (int i = 0; i <= n2; i++) {
            dp[0][i] = true;
        }

        for (int i = 1; i <= n1; i++) {
            for (int j = 1; j <= n2; j++) {
                if (dp[i][j-1]){
                    dp[i][j] = dp[i][j - 1];
                }else {
                    if (s.charAt(i - 1) == t.charAt(j - 1) && dp[i-1][j-1]) {
                        dp[i][j] =dp[i - 1][j - 1];
                    }else {
                        dp[i][j] = dp[i][j - 1];
                    }
                }
            }
        }

        return dp[n1][n2];
    }
}
```

