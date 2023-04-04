---
title: leetcode-785. 判断二分图
date: 2021-05-19 14:01:59
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [785. 判断二分图](https://leetcode-cn.com/problems/is-graph-bipartite/)

```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        // 二分图、染色法判别
        int n = graph.length;
        int[] color = new int[n];

        // 将每个节点都染色一遍
        for(int i = 0; i < n; i++){
            if(color[i] == 0){
                //如果该节点没有被染色
                color[i] = 1;
            }
            if(!dfs(i, 0, graph, color)){
                return false;
            }
        }
        return true;
    }

    public boolean dfs(int cur, int k, int[][] graph, int[] color){

        for(int i = k; i < graph[cur].length; i++){
            int next = graph[cur][i];

            if(color[next] == 0){
                // 没有被染色
                color[next] = 3 - color[cur];
            }else if(color[next] == color[cur]){
                // 染色冲突
                return false;
            }else {
                // 已经被染色并且不冲突
                continue;
            }

            if(!dfs(next, 0, graph, color)){
                return false;
            }
        }
        return true;
    }
}
```

