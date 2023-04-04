---
title: leetcode-面试题 17.10. 主要元素
date: 2021-07-09 11:30:02
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [面试题 17.10. 主要元素](https://leetcode-cn.com/problems/find-majority-element-lcci/)

```java
class Solution {
    public int majorityElement(int[] nums) {
        int count = 0;
        int ans = -1;
        for(int i = 0; i < nums.length; i++){
            if(count == 0){
                ans = nums[i];
            }
            if(nums[i] != ans){
                count--;
            }else{
                count++;
            }
        }

        int cnt = 0;
        for(int i = 0; i < nums.length; i++){
            if(nums[i] == ans){
                cnt++;
            }
        }
        return cnt*2 > nums.length ? ans : -1;  
    }
}
```

