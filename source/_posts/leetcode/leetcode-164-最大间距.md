---
title: leetcode-164. 最大间距
date: 2020-11-26 11:04:18
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-164. 最大间距](https://leetcode-cn.com/problems/maximum-gap/)

```java
class Solution {
    public int maximumGap(int[] nums) {
        Arrays.sort(nums);
        int n = nums.length;
        if(n < 2){
            return 0;
        }
        int max = Integer.MIN_VALUE;
        for(int i = 1; i < n; i++){
            max = Math.max(max, nums[i] - nums[i-1]);
        }
        return max;
    }
}
```

