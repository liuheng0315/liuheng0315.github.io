---
title: leetcode-349. 两个数组的交集
date: 2020-11-02 19:10:59
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-349. 两个数组的交集](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<>();
        Set<Integer> ans = new HashSet<>();
        for(int i = 0; i < nums2.length; i++){
            set.add(nums2[i]);
        }

        for(int i = 0; i < nums1.length; i++){
            if(set.contains(nums1[i])){
                ans.add(nums1[i]);
            }
        }
        int[] result = new int[ans.size()];
        int i = 0 ;
        for(Integer k : ans){
            result[i++] = k;
        }
        return result;
    }
}
```

