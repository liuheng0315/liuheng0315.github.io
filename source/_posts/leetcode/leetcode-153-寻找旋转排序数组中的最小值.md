---
title: leetcode-153. 寻找旋转排序数组中的最小值
date: 2021-04-08 10:53:04
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

```java
class Solution {
    public int findMin(int[] nums) {
        int i = 0;
        int j = nums.length - 1;
        while(i <= j){
            if (i == j) {
                break;
            }
            int mid = (i+j)/2;
            if(nums[mid] > nums[j]){
                i = mid + 1;
            }else{
                j = mid;
            }
        }
        return nums[i];
    }
}
```

