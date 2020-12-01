---
title: leetcode-34. 在排序数组中查找元素的第一个和最后一个位置
date: 2020-12-01 12:41:55
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int n = nums.length;
        int i = -1;
        int j = n - 1;
        int a = -1;
        int b = -1;
        while(i < n-1){
            i++;
            if(nums[i] == target){
                a = i;
                break;
            }
        }

        while(j >= 0){
            if(nums[j] == target){
                b = j;
                break;
            }
            j --;
        }
        return new int[]{a, b};
    }
}
```

