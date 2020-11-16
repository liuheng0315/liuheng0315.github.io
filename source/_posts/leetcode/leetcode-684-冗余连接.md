---
title: leetcode-684. 冗余连接
date: 2020-09-29 13:09:24
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/)

**并查集解决图中只存在一个树的问题**

```java
class Solution {
    int[] parent;
    public int[] findRedundantConnection(int[][] edges) {
        int N = edges.length + 1;
        parent = new int[N];
        int[] result = new int[]{};
        //读入所有边去除删除的边
        for (int i = 0; i < edges.length; i++) {

            // 初始化并查集
            for (int j = 0; j < N; j++) {
                parent[j] = j;
            }

            // 除了i这个边不要
            for (int j = 0; j < edges.length; j++) {
                if (i == j) {
                    continue;
                }

                //连接边
                int[] edge = edges[j];
                union(edge[0], edge[1], parent);
            }

            //判断是否只存在一个根
            int count = 0;
            for (int j = 1; j < N; j++) {
                if (parent[j] == j) {
                    count++;
                }
            }

            if (count == 1) {
                result = edges[i];
            }
        }

        return result;
    }

    public int find(int i , int[] parent){
        if (i != parent[i]) {
            parent[i] = find(parent[i], parent);
        }
        return parent[i];
    }

    public void union(int i, int j,int[] parent){
        int x = find(i, parent);
        int y = find(j, parent);
        if (x != y) {
            parent[x] = y;
        }
    }
}
```

