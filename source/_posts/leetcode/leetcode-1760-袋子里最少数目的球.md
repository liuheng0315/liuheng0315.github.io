---
title: leetcode-1760. 袋子里最少数目的球
date: 2021-02-20 10:26:05
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1760. 袋子里最少数目的球](https://leetcode-cn.com/problems/minimum-limit-of-balls-in-a-bag/)

二分查找递归寻找答案是否满足条件

```java
class Solution {
    public int minimumSize(int[] nums, int maxOperations) {
        int l = 1;
        int r = Integer.MAX_VALUE;
        int ans = 0;
        while (l <= r) {
            int mid = l + (r-l)/2;
            if (check(nums, maxOperations, mid)){
                r = mid -1;
                ans = mid;
            }else{
                l = mid + 1;
            }
        }
        return ans;
    }

    public boolean check(int[] nums, int maxOperations, int mid){
        int count = 0;
        for (int num : nums) {
            if (num % mid == 0){
                count += num/mid -1;
            }else {
                count += num / mid;
            }
        }

        return count <= maxOperations;
    }
}
```