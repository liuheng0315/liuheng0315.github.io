---
title: leetcode-1470. 重新排列数组
date: 2020-10-14 17:34:37
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1470. 重新排列数组](https://leetcode-cn.com/problems/shuffle-the-array/)

```java
class Solution {
    public int[] shuffle(int[] nums, int n) {
        int[] ans = new int[2*n];
        int idx = 0;
        for (int i = 0; i < n; i++) {
            ans[idx++] = nums[i];
            ans[idx++] = nums[n + i];
        }
        return ans;
    }
}
```

