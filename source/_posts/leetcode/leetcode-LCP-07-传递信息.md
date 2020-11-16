---
title: leetcode-LCP 07. 传递信息
date: 2020-10-22 19:52:26
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-LCP 07. 传递信息](https://leetcode-cn.com/problems/chuan-di-xin-xi/)

```java
class Solution {
    int count = 0;

    public int numWays(int n, int[][] relation, int k) {
        dfs(0, n, relation, k);
        return count;
    }

    public void dfs(int cur, int n, int[][] relation, int k) {
        if (k == 0 && cur == n - 1) {
            count++;
            return;
        }

        if (k < 0) {
            return;
        }

        for (int[] arr : relation) {
            if (cur == arr[0]) {
                // 可以进行传递
                dfs(arr[1], n, relation, k-1);
            }
        }
    }
}
```

