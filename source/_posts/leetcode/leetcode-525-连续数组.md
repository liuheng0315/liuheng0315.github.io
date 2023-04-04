---
title: leetcode-525. 连续数组
date: 2021-06-03 15:48:56
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [525. 连续数组](https://leetcode-cn.com/problems/contiguous-array/)

```java
class Solution {
    public int findMaxLength(int[] nums) {
        int n = nums.length;
        int[] s = new int[n+1];
        for(int i = 1; i <= n; i++){
            if(nums[i-1] == 0){
                s[i] = s[i-1] -1;
            }else{
                s[i] = s[i-1] + nums[i-1];
            }
        }

        int ans = 0;
        Map<Integer, Integer> map = new HashMap<>();

        for(int i = 0; i <n; i++){
            if(s[i+1] == 0){
                ans = Math.max(ans, i+1);
            }else if(map.containsKey(s[i+1])){
                ans = Math.max(ans, i - map.get(s[i+1]));
            }else{
                map.put(s[i+1], i);
            }
        }
        
        return ans;
    }
}
```

