---
title: leetcode-81. 搜索旋转排序数组 II
date: 2021-04-07 09:12:26
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [81. 搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

```java
class Solution {
    public boolean search(int[] nums, int target) {
        if(nums.length == 0){
            return false;
        }
        // 比较前后两个数
        int frist = nums[0];
        int last = nums[nums.length-1];
        if(target == frist || target == last){
            return true;
        }

        if(target > frist){
            // 从左到右查找
            for(int i = 1; i < nums.length; i++){
                if(nums[i] == target){
                    return true;
                }
                if(nums[i] < nums[i-1]){
                    return false;
                }
            }
        }

        if(target < last){
            // 从右向左查找
            for(int i = nums.length - 2; i >= 0; i--){
                if(nums[i] == target){
                    return true;
                }
                if(nums[i] > nums[i+1]){
                    return false;
                }
            }
        }
        return false;
    }
}
```

