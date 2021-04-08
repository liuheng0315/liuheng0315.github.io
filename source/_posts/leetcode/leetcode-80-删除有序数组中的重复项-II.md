---
title: leetcode-80. 删除有序数组中的重复项 II
date: 2021-04-06 09:11:53
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [80. 删除有序数组中的重复项 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int n = nums.length;
        if(n < 3){
            return n;
        }
        int last = nums[nums.length - 1];
        int i = 0;
        for(i = 2; i < n; i++){
            if(nums[i] < nums[i-1]){
                break;
            }
            if(nums[i-1] == nums[i] && nums[i] == nums[i- 2]){
                move(nums, i);
                if(nums[nums.length - 1] == last){
                    break;
                }
                i--;
            }
        }
        return i;
    }

    // 移动数组
    public void move(int[] nums, int idx){
        int tmp = nums[idx];
        for(int i = idx + 1; i < nums.length; i++){
            nums[i - 1] = nums[i];
        }
        nums[nums.length - 1] = tmp;
    }
}
```

