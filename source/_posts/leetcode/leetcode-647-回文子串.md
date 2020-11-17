---
title: leetcode-647. 回文子串
date: 2020-10-13 18:14:29
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-647. 回文子串](https://leetcode-cn.com/problems/palindromic-substrings/)

**原始思路**

```java
class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        int[] dp = new int[n+1];
        // 初始化
        dp[1] = 1;

        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + 1;
            for (int j = i - 1; j > 0; j--) {
                String str = s.substring(j - 1, i);
                if (judge(str)) {
                    dp[i] += 1;
                }
            }
        }
        return dp[n];
    }

    public boolean judge(String s) {
        int i = 0;
        int j = s.length() - 1;
        while (i < j) {
            if (s.charAt(i) != s.charAt(j)) {
                return false;
            }
            i++;
            j--;
        }
        return true;
    }
}
```



**动规解法**

```java
class Solution {
    public int countSubstrings(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        int count = 0;
        for (int l = 0; l < n; l++) {
            for (int i = 0; i + l < n; i++) {
                int j = i + l;
                if (l == 0) {
                    dp[i][j] = true;
                } else if (l == 1) {
                    dp[i][j] = s.charAt(i) == s.charAt(j);
                }else {
                    dp[i][j] = s.charAt(i) == s.charAt(j) && dp[i + 1][j - 1];
                }

                if (dp[i][j]) {
                    count++;
                }
            }
        }
        return count;
    }
}
```

