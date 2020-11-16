---
title: leetcode-474. 一和零
date: 2020-10-21 22:42:57
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-474. 一和零](https://leetcode-cn.com/problems/ones-and-zeroes/)

**原始思路**

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int n1 = strs.length;
        int[][][] dp = new int[n1+1][m+1][n+1];
        for (int i = 1; i <= strs.length; i++) {
            int[] arr = getZeorAndOne(strs[i-1]);
            int zero = arr[0];
            int one = arr[1];
            for (int j = 0; j <= m; j++) {
                for (int k = 0; k <= n; k++) {
                    dp[i][j][k] = dp[i-1][j][k];
                    if (zero <= j && one <= k){
                        dp[i][j][k] = Math.max(dp[i][j][k], dp[i-1][j - zero][k - one] + 1);
                    }
                }
            }
        }
        return dp[n1][m][n];
    }

    public int[] getZeorAndOne(String str){
        int[] arr = new int[2];
        int i = 0;
        int j = 0;
        for (int k = 0; k < str.length(); k++) {
            if (str.charAt(k) == '0'){
                i++;
            }else {
                j++;
            }
        }
        arr[0] = i;
        arr[1] = j;
        return arr;
    }
}
```



**状态压缩**

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int n1 = strs.length;
        int[][] dp = new int[m+1][n+1];
        for (int i = 1; i <= strs.length; i++) {
            int[] arr = getZeorAndOne(strs[i-1]);
            int zero = arr[0];
            int one = arr[1];
            for (int j = m; j >= 0; j--) {
                for (int k = n; k >= 0; k--) {
                    if (zero <= j && one <= k){
                        dp[j][k] = Math.max(dp[j][k], dp[j - zero][k - one] + 1);
                    }
                }
            }
        }
        return dp[m][n];
    }

    public int[] getZeorAndOne(String str){
        int[] arr = new int[2];
        int i = 0;
        int j = 0;
        for (int k = 0; k < str.length(); k++) {
            if (str.charAt(k) == '0'){
                i++;
            }else {
                j++;
            }
        }
        arr[0] = i;
        arr[1] = j;
        return arr;
    }
}
```

