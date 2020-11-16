---
title: leetcode-516. 最长回文子序列
date: 2020-10-14 15:45:57
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

**题解思路**

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        int n = s.length();
        int[][] dp = new int[n][n];

        // 初始化dp
        for (int i = 0; i < n; i++) {
                dp[i][i] = 1;
        }

        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                }else {
                    dp[i][j] = Math.max(dp[i][j - 1], dp[i + 1][j]);
                }
            }
        }
        return dp[0][n-1];
    }
}
```

