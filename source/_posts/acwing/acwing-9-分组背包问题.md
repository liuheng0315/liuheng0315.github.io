---
title: acwing-9. 分组背包问题
date: 2020-09-21 00:15:11
categories: 
		- 算法
tags: 
	- 动态规划
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-9. 分组背包问题](https://www.acwing.com/problem/content/description/9/)

```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int V = sc.nextInt();
        int[][] weight = new int[103][103];
        int[][] value = new int[103][103];
        for(int i = 1; i<= N; i++){
            int k = sc.nextInt();
            for(int j = 1; j <= k; j++){
                weight[i][j] = sc.nextInt();
                value[i][j] = sc.nextInt();
            }
        }
        int result = getMaxValue(N, V, weight, value);
        System.out.println(result);
    }
    
    public static int getMaxValue(int N, int V, int[][] weight, int[][] value){
        int[][] dp = new int[N+1][V+1];
        for(int i = 1; i <= N; i++){
            for(int j = 1; j <= V; j++){
                dp[i][j] = dp[i-1][j];
                for(int k = 1; k <= weight[i].length && weight[i][k]!=0; k++){
                    if(j >= weight[i][k]){
                       dp[i][j] = Math.max(dp[i][j], dp[i-1][j-weight[i][k]] + value[i][k]); 
                    }
                }
            }
        }
        return dp[N][V];
    }
}
```

