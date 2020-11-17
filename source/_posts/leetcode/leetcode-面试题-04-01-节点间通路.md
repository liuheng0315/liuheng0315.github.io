---
title: leetcode-面试题 04.01. 节点间通路
date: 2020-09-28 16:32:27
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-面试题 04.01. 节点间通路](https://leetcode-cn.com/problems/route-between-nodes-lcci/)

```java
class Solution {
    List<Set<Integer>> g = new ArrayList<>();
    public boolean findWhetherExistsPath(int n, int[][] graph, int start, int target) {
        for (int i = 0; i < n; i++) {
            g.add(new HashSet<Integer>());
        }

        for (int[] arr :
                graph) {
            g.get(arr[0]).add(arr[1]);
        }

        return dfs(n, start, start, target);
    }

    public boolean dfs(int n, int cur, int start, int target) {
        if (cur == target) {
            return true;
        }

        //遍历邻接矩阵
        for (Integer i: g.get(cur)) {
            boolean bool = dfs(n, i, start, target);
            if (bool) {
                return true;
            }
        }
        return false;
    }
}

```

