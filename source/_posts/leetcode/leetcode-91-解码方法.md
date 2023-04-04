---
title: leetcode-91. 解码方法
date: 2021-04-21 22:53:08
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [91. 解码方法](https://leetcode-cn.com/problems/decode-ways/)

```java
class Solution {
    public int numDecodings(String s) {
        int n = s.length();
        int[] dp = new int[n + 1];
        // 初始化dp
        if (s.startsWith("0")) {
            return 0;
        } else {
            dp[1] = 1;
            if (n == 1) {
                return dp[1];
            }
            if (s.charAt(1) == '0') {
                if (Integer.valueOf(s.substring(0, 2)) > 26) {
                    return 0;
                }
                dp[2] = dp[1];
            } else {
                if (Integer.valueOf(s.substring(0, 2)) > 26) {
                    dp[2] = dp[1];
                } else {
                    dp[2] = dp[1] + 1;
                }
            }
            for (int j = 2; j <= n; j++) {
                String str = s.substring(j - 2, j);
                if (s.charAt(j - 1) == '0') {
                    if (Integer.valueOf(str) <= 26 && !str.startsWith("0")) {
                        dp[j] = Math.max(dp[j], dp[j - 2]);
                    } else if (str.startsWith("0") || Integer.valueOf(str) > 26) {
                        return 0;
                    }
                } else if (s.charAt(j - 1) != '0') {
                    dp[j] = Math.max(dp[j], dp[j - 1]);
                    if (Integer.valueOf(str) <= 26 && !str.startsWith("0")) {
                        dp[j] = Math.max(dp[j], dp[j - 2] + dp[j - 1]);
                    }
                }
            }
            return dp[n];
        }
    }
}
```

