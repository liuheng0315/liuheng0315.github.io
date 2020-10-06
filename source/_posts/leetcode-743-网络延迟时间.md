---
title: leetcode-743. 网络延迟时间
date: 2020-09-30 17:12:26
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-743. 网络延迟时间](https://leetcode-cn.com/problems/network-delay-time/)

**floyd算法求多源最短路**

```java
class Solution {
    int[][] dist;
    int inf = 1000000000;
    
    public int networkDelayTime(int[][] times, int N, int K) {
        dist = new int[N + 1][N + 1];
        for (int i = 0; i < N + 1; i++) {
            for (int j = 0; j < N + 1; j++) {
                if (i == j) {
                    // 自己到自己距离为0
                    dist[i][j] = 0;
                } else {
                    dist[i][j] = inf;
                }
            }
        }

        // 读入数据
        for (int[] arr : times) {
            int a = arr[0];
            int b = arr[1];
            int c = arr[2];
            dist[a][b] = Math.min(dist[a][b], c);
        }

        floyd(N);

        int result = 0;
        for (int i = 1; i <= N; i++) {
            if (i == K) {
                continue;
            }
            result = Math.max(result, dist[K][i]);
        }
        return result > inf/2 ? -1 : result;
    }

    public void floyd(int N){
        for (int k = 1; k <= N; k++){
            for (int i = 1; i <= N; i++) {
                for (int j = 1; j <= N; j++) {
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
}
```



**朴素dijkstra算法**

```java
class Solution {
    // 使用邻接矩阵存储图
    int[][] g;
    int[] dist;
    boolean[] st;
    int inf = 100000000;
    public int networkDelayTime(int[][] times, int N, int K) {
        
        int n = times.length;
        g = new int[N+1][N+1];
        dist = new int[N+1];
        st = new boolean[N+1];

        //初始化距离
        Arrays.fill(dist, inf);

        dist[K] = 0;

        //初始化图
        for(int i = 0; i < g.length; i++){
            Arrays.fill(g[i], inf);
        }

        //读入图数据
        for(int i = 0; i < times.length; i++){
            int a = times[i][0];
            int b = times[i][1];
            int c = times[i][2];
            // 只保留最小的边
            g[a][b] = Math.min(g[a][b], c);
        }

        dijkstra(N, K);

        int result = Integer.MIN_VALUE;

        for(int i = 1; i <= N; i++){
            result = Math.max(result, dist[i]);
        }
        return result == inf ? -1 : result;
    }

    //使用朴素dijkstra
    public void dijkstra(int N, int K){
        for(int i = 0; i < N; i++){
            //找出距离K点最小的点
            int t = -1;
            for(int j = 1; j <= N; j++){                
                if(!st[j] && (t== -1 || dist[j] < dist[t])){
                    t = j;
                }
            }

            st[t] = true;

            // 使用t更新其它的点
            for(int j = 1; j <=N; j++){
                dist[j] = Math.min(dist[j],g[t][j]+dist[t]);
            }
        }
    }
}
```



**堆优化版dijkstra算法**

```java
class Solution {
    // 使用邻接表存储图
    int[] e;
    int[] w;
    int[] ne;
    int[] h;
    int idx = 0;

    int[] dist;
    boolean[] st;
    int inf = 100000000;
    PriorityQueue<int[]> queue = new PriorityQueue<>((o1,o2)->{
        return o1[1]-o2[1];
    });
    public int networkDelayTime(int[][] times, int N, int K) {
        // 初始化邻接表
        int n = times.length + 10;
        e = new int[n];
        w = new int[n];
        ne = new int[n];
        h = new int[n];

        Arrays.fill(h, -1);

        // 初始化距离
        dist = new int[N+1];
        Arrays.fill(dist, inf);
        // K点距离起始点的路径为0
        dist[K] = 0;

        // 初始化是否访问
        st = new boolean[N+1];

        // 读入图数据
        for(int i = 0; i < times.length; i++){
            int a = times[i][0];
            int b = times[i][1];
            int c = times[i][2];
            add(a, b, c);
        }

        dijstra(K);

        int result = Integer.MIN_VALUE;

        for(int i = 1; i <= N; i++){
            result = Math.max(result, dist[i]);
        }

        return result == inf ? -1 : result;
    }

    // 堆优化版的dijkstra算法
    public void dijstra(int K){
        // 将起始点放入最小堆中
        queue.offer(new int[]{K, 0});
        while(!queue.isEmpty()){
            int[] arr = queue.poll();
            int t = arr[0];
            int distance = arr[1];
            //将t置为true，表示t这个节点已经访问过了
            st[t] = true;

            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                if(dist[j] > distance + w[i]){
                    dist[j] = distance + w[i];
                    //对加入的节点进行入队
                    if(!st[j]){
                        queue.offer(new int[]{j, dist[j]});
                    }
                }
            }
        }
    }

    public void add(int a,int b,int c){
        e[idx] = b;
        w[idx] = c;
        ne[idx] = h[a];
        h[a] = idx++;
    }
}
```



**Bellman-ford算法**

```java
class Solution {
    // 使用bellman-ford算法
    int N = 10000;
    Graph[] g = new Graph[N];
    int[] dist = new int[N];
    public int networkDelayTime(int[][] times, int N, int K) {
        int m = times.length;
        // 读入图数据
        for(int i = 0; i < times.length; i++){
            int a = times[i][0];
            int b = times[i][1];
            int c = times[i][2];
            g[i] = new Graph(a, b, c);
        }

        bellman(g, N, K, m);

        int result = Integer.MIN_VALUE;
        for(int i = 1; i <= N; i++){
            result = Math.max(result, dist[i]);
        }

        return result == 1000000000 ? -1 : result;
    }

    public void bellman(Graph[] g, int N, int K, int m){
        // 初始化边距
        Arrays.fill(dist, 1000000000);

        // 从K点出发,所以dist[K] = 0;
        dist[K] = 0;

        // 最多进行N-1次边松弛
        for(int i = 1; i < N; i++){
            // 每次使用上一次的结果进行计算,避免数据串联
            int[] copy = Arrays.copyOf(dist, dist.length);
            // 每次遍历所有边
            for(int j = 0; j < m; j++){
                int a = g[j].a;
                int b = g[j].b;
                int c = g[j].c;
                dist[b] = Math.min(dist[b], copy[a]+c);
            }
        }
    }
}

// 定义一个储存图数据的结构
class Graph{
    public int a;
    public int b;
    public int c;
    public Graph(int a,int b, int c){
        this.a = a;
        this.b = b;
        this.c = c;
    }
}
```

