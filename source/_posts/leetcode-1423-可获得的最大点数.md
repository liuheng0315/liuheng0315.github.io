---
title: leetcode-1423. 可获得的最大点数
date: 2020-10-09 18:44:43
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

**使用滑动数组**

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



