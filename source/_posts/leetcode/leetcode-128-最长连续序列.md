---
title: leetcode-128. 最长连续序列
date: 2021-07-23 17:41:22
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        // 并查集
        int n = nums.length;
        int[] parent = new int[n];
        
        Map<Integer, Integer> map = new HashMap<>();
        // 初始化并查集
        for(int i = 0; i < n; i++){
            parent[i] = i;
            if(map.containsKey(nums[i])){
                continue;
            }
            map.put(nums[i], i);
        }

        for(int i = 0; i < n; i++){
            int num = nums[i];
            if(map.get(num) == i){
                // 只处理map中包含的index的角标进行去重
                if(map.containsKey(num-1)){
                    int idx = map.get(num-1);
                    uion(i, idx, parent);
                }

                if(map.containsKey(num+1)){
                    int idx = map.get(num+1);
                    uion(i, idx, parent);
                }
            }
        }


        // 遍历查找并查集中最多的集合
        int ans = 0;
        Map<Integer, Integer> countMap = new HashMap<>();
        for(int i = 0; i < n; i++){
            int p = find(i, parent);
            int count = countMap.getOrDefault(p, 0) + 1;
            countMap.put(p, count);
            ans = Math.max(ans, count);
        }
        return ans;
    }

    public int find(int k, int[] parent){
        if(k != parent[k]){
            parent[k] = find(parent[k], parent);
        }
        return parent[k];
    }

    public void uion(int i, int j, int[] parent){
        int x = find(i, parent);
        int y = find(j, parent);
        if(x != y){
            parent[x] = y;
        }
    }
}
```

