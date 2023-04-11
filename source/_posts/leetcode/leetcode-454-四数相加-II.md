---
title: leetcode-454. 四数相加 II
date: 2023-04-12 00:02:32
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/)

```java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        int res = 0;

        // 分治思想
        Map<Integer, Integer> map1 = merge(nums1, nums2);
        Map<Integer, Integer> map2 = merge(nums3, nums4);

        for(Map.Entry<Integer, Integer> entry : map1.entrySet()){
            // 遍历整个集合
            Integer key = entry.getKey();
            Integer value1 = entry.getValue();

            if(map2.containsKey((0 - key))){
                Integer value2 = map2.get((0 - key));
                res += value1 * value2;
            }
        }

        return res;
    }

    public Map<Integer, Integer> merge(int[] nums1, int[] nums2){
        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < nums1.length; i++){
            for(int j = 0; j < nums2.length; j++){
                Integer value = nums1[i] + nums2[j];
                map.put(value, map.getOrDefault(value, 0) + 1);
            }
        }
        return map;
    }
}
```

