---
title: leetcode-1423. 可获得的最大点数
date: 2020-10-09 18:44:43
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1423. 可获得的最大点数](https://leetcode-cn.com/problems/maximum-points-you-can-obtain-from-cards/)

#### **使用滑动数组**

```java
class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int result = 0;
        if (k >= n) {
            //全拿
            for (int i = 0; i < n; i++) {
                result += cardPoints[i];
            }
            return result;
        }

        // 定义滑动数组
        int l = 0;
        int r = n - k - 1;

        int minValue = Integer.MAX_VALUE;
        int x = 0;
        while (r < n) {
            int ans = 0;
            //一直滑动计算最小值
            if (x == 0) {
                for (int i = l; i <= r; i++) {
                    ans += cardPoints[i];
                }
                x = ans;
            }else {
                ans = x - cardPoints[l-1] + cardPoints[r];
                x = ans;
            }

            minValue = Math.min(minValue, ans);

            l++;
            r++;
        }

        int max = 0;
        for (int i = 0; i < n; i++) {
            max += cardPoints[i];
        }

        return max - minValue;
    }
}

```



**前缀和解法**

```java
class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int[] s = new int[n+1];

        for(int i = 1; i <= n; i++){
            s[i] = s[i-1] + cardPoints[i-1];
        }

        int result = 0;
        //一共取k次,则左边取i次,右边取k-i次
        for(int i = 0; i <= k; i++){
            result = Math.max(result,s[i]+s[n]-s[n-k+i]);
        }
        return result;
    }
}
```

