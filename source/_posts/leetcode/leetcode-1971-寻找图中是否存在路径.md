---
title: leetcode-1971. 寻找图中是否存在路径
date: 2023-04-03 00:23:29
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

[leetcode-1971. 寻找图中是否存在路径](https://leetcode.cn/problems/find-if-path-exists-in-graph/description/)

## 解法一：深度优先搜索

```java
import java.util.*;
class Solution {
    public boolean validPath(int n, int[][] edges, int source, int destination) {
        if(source == destination){
            return true;
        }

        if(edges.length == 0){
            return false;
        }

        // 深度优先搜索
        // 存放路径
        Map<Integer, List<Integer>> map = new HashMap<>();
        for(int i = 0; i < edges.length; i++){
            int a = edges[i][0];
            int b = edges[i][1];

            if(map.containsKey(a)){
                map.get(a).add(b);
            }else{
                List<Integer> list = new ArrayList();
                list.add(b);
                map.put(a, list);
            }

            if(map.containsKey(b)){
                map.get(b).add(a);
            }else{
                List<Integer> list = new ArrayList();
                list.add(a);
                map.put(b, list);
            }
        }

        // 进行深度优先搜索
        // set用来存放已经走过的点
        Set<Integer> set = new HashSet<>();

        return dfs(map, set, source, destination);
    }

    public boolean dfs(Map<Integer, List<Integer>> map, Set<Integer> set, int source, int destination){
        if(!map.containsKey(source)){
            return false;
        }
        
        List<Integer> list = map.get(source);

        if(list.contains(destination)){
            return true;
        }

        if(set.contains(source)){
            // 该节点已被访问过
            return false;
        }

        // 记录节点已经被遍历过
        set.add(source);

        for(int i = 0; i < list.size(); i++){
            if(dfs(map, set, list.get(i), destination)){
                return true;
            }
        }

        return false;
    }
}
```

## 解法二：广度优先搜索

```java
import java.util.*;
class Solution {
    public boolean validPath(int n, int[][] edges, int source, int destination) {
        if(source == destination){
            return true;
        }

        if(edges.length == 0){
            return false;
        }

        // 广度优先搜索
        // 存放路径
        List<Integer>[] arr = new List[n];
        boolean[] visited = new boolean[n];

        for(int i = 0 ; i < n ; i++){
            // 初始化存放路径的数组
            arr[i] = new ArrayList<Integer>();
        }

        for(int i = 0; i < edges.length; i++){
            int a = edges[i][0];
            int b = edges[i][1];
            arr[a].add(b);
            arr[b].add(a);
        }
        
        
        Queue<Integer> queue = new ArrayDeque<Integer>();
        queue.offer(source);
        
        while(queue.size() > 0){
            Integer t = queue.poll();
            List<Integer> list = arr[t];

            if(visited[t]){
                continue;
            }

            visited[t] = true;

            if(t == destination){
                return true;
            }

            for(int i = 0; i < list.size(); i++){
                Integer x = list.get(i);
                if(!visited[x]){
                    queue.offer(x);
                }
            }
        }

        return visited[destination];
    }
}
```

## 解法三：并查集

```java
class Solution {
    public boolean validPath(int n, int[][] edges, int source, int destination) {
        int[] parent = new int[n];

        // 初始化并查集
        for(int i = 0; i < n; i++){
            parent[i] = i;
        }

        for(int[] arr : edges){
            int a = arr[0];
            int b = arr[1];
            // 并查集链接
            union(parent, a, b);
        }

        if(find(parent, source) == find(parent, destination)){
            return true;
        }

        return false;
    }

    public static int find(int[] parent, int k){
        if(parent[k] != k){
            parent[k] = find(parent, parent[k]);
        }
        return parent[k];
    }

    public static void union(int[] parent, int i, int j){
        int x = find(parent, i);
        int y = find(parent, j);
        if(x != y){
            parent[x] = y;
        }
    }
}
```

