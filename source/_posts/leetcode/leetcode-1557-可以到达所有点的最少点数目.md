---
title: leetcode-1557. 可以到达所有点的最少点数目
date: 2020-09-28 18:05:08
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1557. 可以到达所有点的最少点数目](https://leetcode-cn.com/problems/minimum-number-of-vertices-to-reach-all-nodes/)

**使用逆向集合存储点的入度**

```java
class Solution {
    public List<Integer> findSmallestSetOfVertices(int n, List<List<Integer>> edges) {
        List<Integer> result = new ArrayList<>();
        List<Set<Integer>> rgraph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            rgraph.add(new HashSet<>());
        }

        for (int i = 0; i < edges.size(); i++) {
            rgraph.get(edges.get(i).get(1)).add(edges.get(i).get(0));
        }

        for (int i = 0; i < n; i++) {
            if (rgraph.get(i).size() == 0) {
                result.add(i);
            }
        }
        return result;
    }
}
```



**题解思路**

```java
class Solution {
    public List<Integer> findSmallestSetOfVertices(int n, List<List<Integer>> edges) {
        List<Integer> ans = new ArrayList<>();
        
        // 存储所有入度不为0的点
        Set<Integer> set = new HashSet<>();
        for(int i = 0; i < edges.size(); i++){
            set.add(edges.get(i).get(1));
        }
        
        for(int i = 0; i < n; i++){
            if(!set.contains(i)){
                ans.add(i);
            }
        }
        return ans;
    }
}
```

