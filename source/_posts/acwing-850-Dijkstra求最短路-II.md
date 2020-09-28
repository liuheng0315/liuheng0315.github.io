---
title: acwing-850. Dijkstra求最短路 II
date: 2020-09-23 23:01:32
categories: 
		- 算法
tags: 
	- 图论
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[acwing-850. Dijkstra求最短路 II](https://www.acwing.com/problem/content/description/852/)

```java
import java.util.*;
class Main{
    // 定义最大数用于初始化数组
    static int N = 1000010;
    // 定义邻结表 h存储边头结点(即一个边节点的index)，e存储边结点，w存储边的权值，ne存储下一个边结点，
    static int[] h = new int[N];
    static int[] e = new int[N];
    static int[] w = new int[N];
    static int[] ne = new int[N];
    // 定义距离
    static int[] dist = new int[N];
    // 定义标记已经访问过的数组
    static boolean[] st = new boolean[N];
    static int idx = 0;
    // 构造一个最小堆得优先队列,存储的数组代表起点到结点的距离
    static PriorityQueue<int[]> queue = new PriorityQueue<>((o1, o2)->{
        return o1[1] - o2[1];
    });
    
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        // 初始化头结点数组h
        Arrays.fill(h, -1);
        for(int i = 1; i <= m; i++){
            int a = sc.nextInt();
            int b = sc.nextInt();
            int c = sc.nextInt();
           	add(a, b, c);
        }
        int result = dijstra(n);
        System.out.println(result == 1000000000 ? -1 : result);
    }
    
    public static int dijstra(int n){
        //初始化距离
        Arrays.fill(dist, 1000000000);
        //初始化第一个节点到自身的距离
        dist[1] = 0;
        //往堆中push第一个元素
        queue.offer(new int[]{1, 0});
        while( queue.size() > 0 ){
            int[] arr = queue.poll();
            int t = arr[0];
            int distance = arr[1];
            if(st[t]){
                continue;
            }
            //标记该结点已经访问过
            st[t] = true;
            
            // 遍历该点对应的所有边节点
            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                if(dist[j] > distance + w[i]){
                    dist[j] = distance + w[i];
                    queue.offer(new int[]{j, dist[j]});
                }
            }
        }
        return dist[n];
    }
    
    public static void add(int a, int b, int c){
        e[idx] = b;
        w[idx] = c;
        ne[idx] = h[a];
        h[a] = idx++;
    }
}
```

