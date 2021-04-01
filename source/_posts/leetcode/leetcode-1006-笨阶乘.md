---
title: leetcode-1006. 笨阶乘
date: 2021-04-01 09:39:02
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1006. 笨阶乘](https://leetcode-cn.com/problems/clumsy-factorial/)

```java
class Solution {
    public int clumsy(int N) {
        int k = N % 4;
        int m = N / 4;
        int ans = 0;
        boolean bool = true;
        while (m > 0) {
            int a = Math.floorDiv(N * (N - 1), N - 2);
            int b = N - 3;
            ans -= a;
            if (bool) {
                ans = Math.abs(ans);
                bool = false;
            }
            ans += b;

            N = N - 4;
            m--;
        }

        if (k == 3) {
            ans -= k * (k - 1)/(k - 2);
            if (bool) {
                ans = Math.abs(ans);
            }
        } else if (k == 2) {
            ans -= k * (k - 1);
            if (bool) {
                ans = Math.abs(ans);
            }
        }else {
            ans -= k;
            if (bool) {
                ans = Math.abs(ans);
            }
        }

        return ans;
    }
}
```

