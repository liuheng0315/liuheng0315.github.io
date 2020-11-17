---
title: leetcode-802. 找到最终的安全状态
date: 2020-09-28 12:52:47
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-802. 找到最终的安全状态](https://leetcode-cn.com/problems/find-eventual-safe-states/)

**出度为零的点是安全的点**

```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        //记录所有安全的点
        Set<Integer> safe = new HashSet<>();

        // 计算所有点的出度,将出度为0的加入到队列中
        LinkedList<Integer> queue = new LinkedList<>();

        //新建两个List存放图正向节点和边的反向节点
        List<Set<Integer>> g = new ArrayList<>();
        List<Set<Integer>> rg = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            g.add(new HashSet<Integer>());
            rg.add(new HashSet<Integer>());
        }

        //向g和rg中存放数据
        for (int i = 0; i < n; i++) {
            if (graph[i].length == 0) {
                // 说明无出边,证明这个点是安全的
                queue.offer(i);
            }
            for (int j : graph[i]) {
                g.get(i).add(j);
                rg.get(j).add(i);
            }
        }

        while (queue.size() > 0) {
            int k = queue.poll();
            safe.add(k);
            // 遍历所有边是k的点
            for (int i : rg.get(k)) {
                // 从i节点所有边中移除k这个点
                g.get(i).remove(k);
                //如果此时i这个点的出入为0,则加入队列
                if (g.get(i).size() == 0) {
                    queue.offer(i);
                }
            }
        }

        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (safe.contains(i)) {
                result.add(i);
            }
        }
        return result;
    }
}
```



**图的深度优先遍历**

```java
class Solution {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        // 定义一个数组存储每个节点的状态,0表示未被遍历,1表示已被遍历但不安全的点,2表示已被遍历但是安全的点
        int[] st = new int[n];
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (dfs(i, graph, st)) {
                result.add(i);
            }
        }
        return result;
    }

    public boolean dfs(int cur, int[][] graph, int[] st) {
        if (st[cur] == 1) {
            return false;
        }
        if (st[cur] == 2) {
            return true;
        }
        st[cur] = 1;
        for (int k : graph[cur]) {
            if (st[k] == 2) {
                continue;
            }
            if (st[k] == 1) {
                return false;
            }
            if (!dfs(k, graph, st)) {
                return false;
            }
        }
        st[cur] = 2;
        return true;
    }
}
```

