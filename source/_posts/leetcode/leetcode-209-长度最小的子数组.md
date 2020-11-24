---
title: leetcode-209. 长度最小的子数组
date: 2020-11-24 09:09:34
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-209. 长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int n = nums.length;
        int[] arr = new int[n+1];
        for (int i = 1; i <= n; i++) {
            arr[i] = arr[i - 1] + nums[i - 1];
        }

        for (int l = 0; l <= n; l++) {
            for (int i = 0; i+l < n; i++) {
                int j = i + l;
                if (arr[j+1] - arr[i] >= s){
                    return j - i + 1;
                }
            }
        }
        return 0;
    }
}
```

