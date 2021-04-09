---
title: leetcode-154. 寻找旋转排序数组中的最小值 II
date: 2021-04-09 09:49:46
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

```java
class Solution {
    public int findMin(int[] nums) {
        int i = 0;
        int j = nums.length - 1;
        while(i < j){
            int mid = (i+j)/2;
            if(nums[mid]>nums[j]){
                i = mid + 1;
            }else if(nums[mid] == nums[j] && nums[i] >= nums[j]){
                i = i + 1;
            }else if(nums[mid] == nums[j] && nums[i] <nums[j]){
                j = mid - 1;
            }else{
                j = mid;
            }
        }
        return nums[i];
    }
}
```

