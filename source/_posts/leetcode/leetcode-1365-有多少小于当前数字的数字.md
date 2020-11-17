---
title: leetcode-1365. 有多少小于当前数字的数字
date: 2020-10-26 09:42:16
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1365. 有多少小于当前数字的数字](https://leetcode-cn.com/problems/how-many-numbers-are-smaller-than-the-current-number/)

```java
class Solution {
    public int[] smallerNumbersThanCurrent(int[] nums) {

        int n = nums.length;
        int[] ans = new int[n];
        Map<Integer, Integer> map = new HashMap<>();
        int tmp = 0;
        int[] nums2= Arrays.copyOf(nums, n);
        Arrays.sort(nums2);
        for (int i = 0; i < n; i++) {
            if (i == 0) {
                map.put(nums2[i], i);
                continue;
            }
            if (nums2[i] == nums2[i-1]){
                map.put(nums2[i], tmp);
            }else {
                tmp = i;
                map.put(nums2[i], i);
            }
        }

        for (int i = 0; i < n; i++) {
            ans[i] = map.get(nums[i]);
        }
        return ans;
    }
}
```

