---
title: acwing- 858. Prim算法求最小生成树
date: 2020-09-30 12:45:15
categories: 
		- 算法
tags: 
	- 图论
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing- 858. Prim算法求最小生成树](https://www.acwing.com/activity/content/code/content/48767/)

```java
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

        System.out.println(prim(n, g, dist, st));
    }
    
    // 将节点不断加入集合中，形成最小树
    public int prim(int n,int[][] g,int[] dist,boolean[] st){
        // 初始化每个点距离集合的距离
        int inf = 1000000000;
        Arrays.fill(dist, inf);
        //统计结果
        int result = 0;
        //每次添加一个节点到集合中，并用该节点更新其它节点
        for(int i = 0; i < n; i++){
            int t = -1;
            //寻找距离集合最小的点
            for(int j = 1; j <= n; j++){
                if(!st[j] && (t == -1 || dist[j] < dist[t])){
                    t = j;
                }
            }
            
            // 将t这个节点加入集合中
            st[t] = true;
            
            // 如果存在不联通的点
            if(i!=0 && dist[t] == inf){
                return inf;
            }
            
            if(i!=0){
                //第一次只把第一个点加入了集合,所有从i=1开始累加
                result += dist[t];
            }
            
            // 使用t这个点去更新其它节点
            for(int j = 1; j <=n; j++){
                dist[j] = Math.min(dist[j],g[t][j]);
            }
        }
        return result;
    }
}
```

