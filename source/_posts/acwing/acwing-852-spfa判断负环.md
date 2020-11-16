---
title: acwing-852. spfa判断负环
date: 2020-09-28 23:22:09
categories: 
		- 算法
tags: 
	- 图论
cover: images/leetcode.jpg
typora-root-url: ..
---

[acwing-852. spfa判断负环](https://www.acwing.com/activity/content/code/content/48499/)

```java
import java.util.*;
class Main{
    static int inf = 1000000000;
    // 定义邻接表
    static int[] e;
    static int[] w;
    static int[] ne;
    static int[] h;
    static int idx = 0;
    //定义距离
    static int[] dist;
    //定义已经访问过的点
    static boolean[] st;
    //定义一个最小堆
    static LinkedList<Integer> queue = new LinkedList<>();
    // 定义一个数组用来计数
    static int[] count;
    public static void main(String[] args) throws Exception{
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        e = new int[m+1];
        w = new int[m+1];
        ne = new int[m+1];
        h = new int[m+1];
        dist = new int[n+1];
        st = new boolean[n+1];
        count = new int[n+1];
        // 初始化
        Arrays.fill(h, -1);
        
        for(int i = 0; i < m; i++){
            int a = sc.nextInt();
            int b = sc.nextInt();
            int c = sc.nextInt();
            
            e[idx] = b;
            w[idx] = c;
            ne[idx] = h[a];
            h[a] = idx++;
        }
        
        boolean result = spfa(n);
        System.out.print(result); 
    }
    
    public static boolean spfa(int n){
        // 初始化距离
        Arrays.fill(dist, inf);
        
        // 将1号点放入queue
        queue.offer(1);
        dist[1] = 0;
        
        st[1] = true;
        
        while(!queue.isEmpty()){
            int t = queue.poll();
            st[t] = false;
            
            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                if(dist[j] > dist[t] + w[i]){
                    dist[j] = dist[t] + w[i];
                    count[j] = count[t] + 1;
                    if(count[j] >= n){
                        // 存在负环
                        return true;
                    }
                    if(!st[j]){
                        //将j加入堆
                        queue.offer(j);
                        st[j] = true;
                    }
                }
            }
        }
        
        return false;
    }
}
```

