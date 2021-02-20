---
title: leetcode-1004. 最大连续1的个数 III
date: 2021-02-19 11:24:46
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1004. 最大连续1的个数 III](https://leetcode-cn.com/problems/max-consecutive-ones-iii/)

```java
class Solution {
    public static int longestOnes(int[] A, int K) {
        int n = A.length;
        int ans = 0;
        int count = 0;
        int i = 0;
        int j = 0;
        while (j < n) {
            if (A[j] == 0) {
                count++;
            }
            
            while (count > K) {
                // 缩小窗口
                if (A[i++] == 0) {
                    count--;
                }
            }
            
            ans = Math.max(ans, j - i + 1);
            j++;
        }
        return ans;
    }
}
```

