---
title: leetcode-5536. 最大网络秩
date: 2020-10-11 14:20:18
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-5536. 最大网络秩](https://leetcode-cn.com/problems/maximal-network-rank/)

**原始思路**

```java
class Solution {
    public int maximalNetworkRank(int n, int[][] roads) {
        Map<Integer, Set<Integer>> graph = new HashMap<>();
        for(int i = 0; i < n; i++){
            graph.put(i, new HashSet<Integer>());
        }

        for(int[] road: roads){
            int a = road[0];
            int b = road[1];
            graph.get(a).add(b);
            graph.get(b).add(a);
        }

        int result = 0;

        //遍历每两个城市
        for(int i = 0; i < n; i++){
            for(int j = i+1; j < n; j++){
                int ans = 0;
                ans += graph.get(i).size();
                ans += graph.get(j).size();
                //去除重叠的边
                if(graph.get(i).contains(j)){
                    ans -= 1;
                }
                result = Math.max(result, ans);
            }
        }

        return result;
    }
}
```

