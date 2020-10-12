---
title: leetcode-338. 比特位计数
date: 2020-10-12 15:43:49
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-338. 比特位计数](https://leetcode-cn.com/problems/counting-bits/)

**动规思路**

```java
class Solution {
    public int[] countBits(int num) {
        int[] dp = new int[num + 1];
        dp[0] = 0;
        if (num == 0) {
            return dp;
        }
        dp[1] = 1;
        if (num == 1) {
            return dp;
        }

        for (int i = 2; i <= num; i++){
            if (((i-1) & 1) == 1) {
                //说明为奇数
                String str = Integer.toBinaryString(i - 1);
                int index = -1;
                int n = str.length();
                for (int j = n-1; j >=0; j--) {
                    if (str.charAt(j) == '0') {
                        index = j;
                        break;
                    }
                }
                if (index == -1) {
                    dp[i] = 1;
                }else {
                    dp[i] = dp[i - 1] - (n-index-1) + 1;
                }
            }else {
                dp[i] = dp[i - 1] + 1;
            }
        }
        return dp;
    }
}
```

