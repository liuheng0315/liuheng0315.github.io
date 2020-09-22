---
title: acwing-849. Dijkstra求最短路 I
date: 2020-09-23 00:29:35
categories: 
		- 算法
tags: 
	- 图论
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[acwing-849. Dijkstra求最短路 I](https://www.acwing.com/problem/content/851/)

```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        int[][] g = new int[501][501];
        int[] dist = new int[501];
        boolean[] st = new boolean[501];
        
        //初始化g
        for(int i = 1; i < g.length; i++){
            Arrays.fill(g[i], 1000000000);
        }
        
        for(int i = 1; i<= m; i++){
            int a = sc.nextInt();
            int b = sc.nextInt();
            int c = sc.nextInt();
            g[a][b] = Math.min(g[a][b], c);
        }
        
        System.out.println(dijkstra(n, g, dist, st));
    }
    
    public static int dijkstra(int n, int[][] g, int[] dist, boolean[] st){
        
        // 所有节点的距离起点的初始值设置为无穷大
        Arrays.fill(dist, 1000000000);
        
        // 起点到起点的距离设置为0
        dist[1] = 0;
        
        // 迭代n次,确定每个点到起点的最短路径
        for(int i = 1; i <= n; i++){
            // 选出最短的路径的点,然后用最短路径的点进行更新
            int t = -1;
            
            for(int j = 1; j<= n; j++){
                //不在st集合中,并且是最短的
                if(!st[j] && (t==-1 || dist[j]<dist[t])){
                    t = j;
                }
            }
            
            // t这个点被遍历过了
            st[t] = true;
            
            // 使用找到的最小点t，并用t去更新其他的点
            for(int k = 1; k <= n; k++){
                dist[k] = Math.min(dist[k], dist[t] + g[t][k]);
            }
        }
        return dist[n]==1000000000 ? -1 : dist[n];
    } 
}
```

