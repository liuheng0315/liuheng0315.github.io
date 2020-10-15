---
title: leetcode-5. 最长回文子串
date: 2020-10-13 23:19:43
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

**题解思路：中心扩散法**

```java
class Solution {
    public String longestPalindrome(String s) {
        String ans = "";
        for(int i = 0; i < s.length(); i++){
            String s1 = palindrome(s, i, i);
            String s2 = palindrome(s, i, i+1);
            ans = ans.length() > s1.length() ? ans : s1;
            ans = ans.length() > s2.length() ? ans : s2;
        }
        return ans;
    }

    // 中心扩散法
    public String palindrome(String s,int l, int r){
        while(l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)){
            // 向两边扩展
            l--;
            r++;
        }
        return s.substring(l+1, r);
    }
}
```



**题解思路：动态规划**

```java
class Solution {
    public String longestPalindrome(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        String ans = "";
        for (int l = 0; l < n; l++) {
            for (int i = 0; i+l < n; i++) {
                int j = i + l;
                if (l == 0) {
                    //i和j的间隔为0,证明只有一个元素
                    dp[i][j] = true;
                } else if (l == 1) {
                    dp[i][j] = s.charAt(i) == s.charAt(j);
                } else {
                    dp[i][j] = s.charAt(i) == s.charAt(j) && dp[i + 1][j - 1];
                }

                if (dp[i][j] && l + 1 > ans.length()) {
                    ans = s.substring(i, j + 1);
                }
            }
        }
        return ans;
    }
}
```

