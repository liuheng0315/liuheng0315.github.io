---
title: leetcode-347.前K个高频元素
date: 2020-09-07 12:55:32
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-347. 前 K 个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)

**原始思路**

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        // 申请一个map
        Map<Integer, Integer> map = new HashMap<>();

        for (int i = 0; i < nums.length ; i++) {
            if (map.containsKey(nums[i])){
                map.put(nums[i], map.get(nums[i]) + 1);
            }else {
                map.put(nums[i], 1);
            }
        }

        //创建一个最大为k的最大堆
        PriorityQueue<Map.Entry<Integer, Integer>> queue = new PriorityQueue<>(k, new Comparator<Map.Entry<Integer, Integer>>() {
            @Override
            public int compare(Map.Entry<Integer, Integer> o1, Map.Entry<Integer, Integer> o2) {
                return o2.getValue().compareTo(o1.getValue());
            }
        });

        for (Map.Entry<Integer, Integer> entry : map.entrySet()){
            queue.add(entry);
        }

        int[] result = new int[k];
        for (int i = 0; i < k ; i++) {
            result[i] = queue.poll().getKey();
        }
        return result;
    }
}
```

