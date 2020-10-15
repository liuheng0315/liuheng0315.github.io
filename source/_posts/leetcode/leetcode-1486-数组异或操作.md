---
title: leetcode-1486. 数组异或操作
date: 2020-10-14 18:35:14
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1486. 数组异或操作](https://leetcode-cn.com/problems/xor-operation-in-an-array/)

```java
class Solution {
    public int xorOperation(int n, int start) {
        int[] nums = new int[n];
        for (int i = 0; i < n; i++) {
            int k = start + 2 * i;
            nums[i] = k;
        }

        int ans = nums[0];
        for (int i = 1; i < n; i++) {
            ans = (ans ^ nums[i]);
        }

        return ans;
    }
}
```

