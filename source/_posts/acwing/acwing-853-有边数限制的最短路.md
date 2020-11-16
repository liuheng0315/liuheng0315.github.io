---
title: acwing-853. 有边数限制的最短路
date: 2020-09-25 13:42:19
categories: 
		- 算法
tags: 
	- 图论
cover: images/leetcode.jpg
typora-root-url: ..
---

[acwing-853. 有边数限制的最短路](https://www.acwing.com/problem/content/855/)

```java
import java.util.*;
class Main{
    static int N = 10010;
    //存放图数据
    static Node[] node = new Node[N];
    //定义距离
    static int[] dist = new int[N];
    static int inf = 1000000000;
    
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        int k = sc.nextInt();
        
        for(int i = 1; i <= m; i++){
            int a = sc.nextInt();
            int b = sc.nextInt();
            int c = sc.nextInt();
            node[i] = new Node(a, b, c);
        }
        
        int result = bellmanFord(node, n, m, k);
        if(result == -1){
            System.out.print("impossible");
        }else{
            System.out.print(result);
        }
    }
    
    
    public static int bellmanFord(Node[] node, int n, int m, int k){
        //初始化距离
        Arrays.fill(dist, inf);
        dist[1] = 0;
        for(int i = 1; i <= k; i++){
            // copy距离
            int[] d = Arrays.copyOf(dist, N);
            
            // 遍历每一条边进行松弛
            for(int j = 1; j <= m; j++){
                int a = node[j].a;
                int b = node[j].b;
                int c = node[j].c;
                dist[b] = Math.min(dist[b], d[a] + c);
            }
        }
        
        return dist[n] > inf/2 ? -1 : dist[n];
    } 
}

//bellman-ford算法使用一个结果存储图
class Node{
    public int a;
    public int b;
    public int c;
    public Node(int a, int b, int c){
        this.a = a;
        this.b = b;
        this.c = c;
    }
}    
```

