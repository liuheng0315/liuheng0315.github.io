---
title: leetcode-剑指 Offer 13. 机器人的运动范围
date: 2021-03-19 09:14:39
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [剑指 Offer 13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

```java
class Solution {
    int ans = 0;
    Set<Integer> set = new HashSet<>();
    public int movingCount(int m, int n, int k) {
        dfs(0, 0, m, n, k);
        return ans;
    }

    public void dfs(int i, int j, int m, int n, int k) {
        if (judge(i, j, m, n, k)){
            if (!set.contains(i * n + j)){
                ans++;
                set.add(i * n + j);
            }else {
                return;
            }
            dfs(i + 1, j, m, n, k);
            dfs(i, j + 1, m, n, k);
        }
    }

    public boolean judge(int i, int j, int m, int n, int k) {
        if (i < 0 || i >= m || j < 0 || j >= n) {
            return false;
        }
        if (getNum(i, j) > k) {
            return false;
        }
        return true;
    }

    public int getNum(int m, int n) {
        int num = 0;
        while (m != 0) {
            num += m % 10;
            m = m / 10;
        }

        while (n != 0) {
            num += n % 10;
            n = n / 10;
        }
        return num;
    }
}
```

