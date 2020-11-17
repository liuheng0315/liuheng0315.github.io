---
title: leetcode-1403. 非递增顺序的最小子序列
date: 2020-09-22 10:56:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1403. 非递增顺序的最小子序列](https://leetcode-cn.com/problems/minimum-subsequence-in-non-increasing-order/)

```java
class Solution {
    public List<Integer> minSubsequence(int[] nums) {
        Arrays.sort(nums);

        int total = 0;
        for (int i = nums.length - 1; i >= 0; i--) {
            total += nums[i];
        }

        int curTotal = 0;
        List<Integer> result = new ArrayList<>();
        for (int i = nums.length - 1; i >= 0; i--) {
            result.add(nums[i]);
            curTotal += nums[i];
            if (curTotal > total - curTotal) {
                break;
            }
        }
        return result;
    }
}
```

