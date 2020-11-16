---
title: leetcode-1024. 视频拼接
date: 2020-10-24 13:55:55
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1024. 视频拼接](https://leetcode-cn.com/problems/video-stitching/)

**双指针**

```java
class Solution {
    public int videoStitching(int[][] clips, int T) {
        int count = 0;
        int l = 0;
        int r = 0;
        while (r < T) {
            boolean bool = false;
            int l2 = l;
            int r2 = r;
            for (int[] clip : clips) {
                int a = clip[0];
                int b = clip[1];
                if (a <= r2 && a >= l2 && b > r) {
                    // 存在这个空间
                    bool = true;
                    r = b;
                    l = a;
                }
            }

            if (bool) {
                count++;
                if (r >= T) {
                    return count;
                }
            }else {
                return -1;
            }
        }

        return count;
    }
}
```



**题解思路dp**

```java
class Solution {
    public int videoStitching(int[][] clips, int T) {
        int[] dp = new int[T+1];
        Arrays.fill(dp, Integer.MAX_VALUE-1);
        dp[0] = 0;
        //记录最有的区间
        for (int i = 1; i <= T; i++) {
            for (int[] clip : clips) {
                int a = clip[0];
                int b = clip[1];
                if (i >= a && i <= b) {
                    dp[i] = Math.min(dp[i], dp[a] + 1);
                }
            }
        }
        return dp[T] == Integer.MAX_VALUE-1 ? -1 : dp[T];
    }
}
```

