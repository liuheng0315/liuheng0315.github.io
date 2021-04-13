---
title: acwing-91. 最短Hamilton路径
date: 2021-04-01 11:53:14
categories: 
		- algorithm
tags: 
	- 位运算
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-91. 最短Hamilton路径](https://www.acwing.com/problem/content/93/)

```java
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[][] weight = new int[n][n];
        
        // 写入数据
        for(int i = 0; i < n; i++){
            for(int j = 0; j < n; j++){
                weight[i][j] = sc.nextInt();
            }
        }
        
        System.out.println(hamilton(n, weight));
    }
    
    public static int hamilton(int n, int[][] weight){
        
        // 定义点到点的边距
        int[][] dist = new int[1<<n][n];
        // 初始化边距
        for(int i = 0; i < (1 << n); i++){
            Arrays.fill(dist[i], Integer.MAX_VALUE >> 1);
        }
        // 还没出发则为0
        dist[1][0] = 0;
        
        for(int i = 0; i < (1 << n); i++){
            for(int j = 0; j < n; j++){
                // 到达j这个点的最短距离
                if((i>>j & 1) == 1){
                    for(int k = 0; k < n; k++){
                        // 从k这个点走到j这个点
                        if ((i >> k & 1) == 1){
                            dist[i][j] = Math.min(dist[i][j], dist[i-(1<<j)][k] + weight[k][j]);
                        }
                    }
                }
            }
        }
        
        return dist[(1<<n) - 1][n-1];
    }
}
```

