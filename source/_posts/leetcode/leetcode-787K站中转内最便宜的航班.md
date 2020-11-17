---
title: leetcode-787K站中转内最便宜的航班
date: 2020-09-16 19:17:41
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [787. K 站中转内最便宜的航班](https://leetcode-cn.com/problems/cheapest-flights-within-k-stops/)

**使用状态转移dp**

```java
class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {

        //n目的地,最多经过K次中转,说明最多走K + 1 次航班,
        int[][] dp = new int[n][K + 2];

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < K + 2; j++) {
                dp[i][j] = 100000000;
            }
        }

        dp[src][0] = 0;

        for (int i = 1; i <= K + 1; i++) {
            dp[src][i] = 0;
            for (int[] flight : flights) {
                dp[flight[1]][i] = Math.min(dp[flight[1]][i], dp[flight[0]][i-1] + flight[2]);
            }
        }

        return dp[dst][K + 1] == 100000000 ? -1 : dp[dst][K + 1];
    }
}
```



**Bellman-ford算法**

```java
class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {
        int[] dist = new int[n+1];
        // 初始化距离
        Arrays.fill(dist, 1000000000);
        dist[src] = 0;
        // 只能经过K个中转
        for(int i = 0; i <= K; i++){
            // copy数组,避免发生数据串联
            int[] ds = Arrays.copyOf(dist, dist.length);
            // 每次都遍历所有航班
            for(int[] flight: flights){
                int a = flight[0];
                int b = flight[1];
                int c = flight[2];
                if(dist[b] > ds[a] + c){
                    dist[b] = Math.min(dist[b], ds[a] + c);
                }
            }
        }
        return dist[dst] > 1000000000/2 ? -1 : dist[dst];
    }
}
```
<<<<<<< HEAD
=======



**dijkstra堆优化版算法**

```java
class Solution {
    // 定义图的邻接表
    int inf = 1000000000;
    int n = 100000;
    int[] e = new int[n];
    int[] w = new int[n];
    int[] ne = new int[n];
    int[] h = new int[n];
    int index = 0;
    
    // 定义存储最短的数组、节点是否访问
    int[] dist = new int[n];
    boolean[] bool = new boolean[n];
    
    int count = 0;
    
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {
    	// 将数据读入图
        for(int[] flight: flights){
            e[index] = flight[1];
            w[index] = flight[2];
            ne[index] = h;
            h[flight[0]] = idx ++;
        }
        
        // 初始化距离
        Arrays.fill(dist, inf);
        
        // 定义一个最小堆,点到起点的距离
        PriorityQueue<int[]> queue = new PriorityQueue<>((o1, o2)->{
            return o1[2] - o2[2];
        });
        
        // 加入初始值
        queue.offer(new int[]{src, 0});
        while(queue.size() > 0){
            int[] arr = queue.poll();
            int t = arr[0];
            int distance = arr[1];
            count ++;
            if(bool[t] || count > K + 1){
                continue;
            }
            bool[t] = true;
            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                if(dist[j] > dist[i] + w[i]){
                    dist[j] = distance + w[i];
                    // 进行入队
                    queue.offer(new int[]{j, dist[j]});
                }
            }
        }
        
        return dist[dst];
    }
}
```

>>>>>>> 1e1a063b2ed2902f22f9c10fe1fce8bce47a145d
