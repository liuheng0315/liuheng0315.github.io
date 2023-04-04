---
title: leetcode-523. 连续的子数组和
date: 2021-06-03 17:37:10
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [523. 连续的子数组和](https://leetcode-cn.com/problems/continuous-subarray-sum/)

```java
class Solution {
    public boolean checkSubarraySum(int[] nums, int k) {
        int n = nums.length;
        int[] s = new int[n+1];
        for(int i = 1; i <= n; i++){
            s[i] = s[i-1] + nums[i-1];
        }
        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < n; i++){
            if(s[i+1]%k == 0 && i >= 1){
                return true;
            }else if(map.containsKey(s[i+1]%k)){
                int x = map.get(s[i+1]%k);
                if(i - x >= 2){
                    return true;
                }
            }else{
                map.put(s[i+1]%k, i);
            }
        }

        return false;
    }
}
```

