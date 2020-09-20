---
title: acwing-2. 01背包问题
date: 2020-09-17 23:13:50
categories: 
		- 算法
tags: 
	- 动态规划
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[acwing-2. 01背包问题](https://www.acwing.com/problem/content/2/)

**二维数组dp**

```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int V = sc.nextInt();
        int[] w = new int[N + 1];
        int[] value = new int[N + 1];
        for(int i = 1; i<= N; i++){
            w[i] = sc.nextInt();
            value[i] = sc.nextInt();
        }
        int res = getMaxValue(N, w, value, V);
        System.out.print(res);
    }
    
    public static int getMaxValue(int n, int[] w, int[] value, int V){
        // dp中存的是最大价值
        int[][] dp = new int[n + 1][V + 1];
        
        for(int i = 1; i <= n; i++){
            for(int j = 1; j <= V; j++){
                dp[i][j] = dp[i-1][j];
                if(j >= w[i]){
                    dp[i][j] = Math.max(dp[i][j], dp[i-1][j-w[i]] + value[i]);
                }
            }
        }
        return dp[n][V];
    }
}
```



**滚动数组压缩**

```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int V = sc.nextInt();
        int[] w = new int[N + 1];
        int[] value = new int[N + 1];
        for(int i = 1; i<= N; i++){
            w[i] = sc.nextInt();
            value[i] = sc.nextInt();
        }
        int res = getMaxValue(N, w, value, V);
        System.out.print(res);
    }
    
    public static int getMaxValue(int n, int[] w, int[] value, int V){
        // dp中存的是最大价值
        int[] dp = new int[V + 1];
        
        for(int i = 1; i <= n; i++){
            for(int j = V; j >= w[i]; j--){
            	dp[j] = Math.max(dp[j], dp[j-w[i]] + value[i]);
            }
        }
        return dp[V];
    }
}
```

