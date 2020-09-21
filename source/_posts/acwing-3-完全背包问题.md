---
title: acwing-3. 完全背包问题
date: 2020-09-18 09:57:08
categories: 
		- 算法
tags: 
	- 背包问题
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[acwing-3. 完全背包问题](https://www.acwing.com/problem/content/description/3/)


```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int w = sc.nextInt();
        
        int[] weight = new int[n+1];
        int[] value = new int[n+1];
        for(int i = 1; i<= n; i++){
            weight[i] = sc.nextInt();
            value[i] = sc.nextInt();
        }
        System.out.print(getMax(n, w, weight, value));
    }
    
    public static int getMax(int n, int w, int[] weight, int[] value){
        int[][] dp = new int[n+1][w+1];
        for(int i = 1; i <= n; i++){
            for(int j = 1; j <= w; j++){
                for(int k = 0; k*weight[i] <= j; k++){
                    dp[i][j] = Math.max(dp[i][j], dp[i-1][j-k*weight[i]] + k * value[i]);
                }
            }
        }
        return dp[n][w];
    }
}
```



**状态压缩**

```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int w = sc.nextInt();
        
        int[] weight = new int[n+1];
        int[] value = new int[n+1];
        for(int i = 1; i<= n; i++){
            weight[i] = sc.nextInt();
            value[i] = sc.nextInt();
        }
        System.out.print(getMax(n, w, weight, value));
    }
    
    public static int getMax(int n, int w, int[] weight, int[] value){
        int[] dp = new int[w+1];
        for(int i = 1; i <= n; i++){
            for(int j = weight[i]; j <= w; j++){
                   dp[j] = Math.max(dp[j], dp[j-weight[i]] + value[i]);
            }
        }
        return dp[w];
    }
}
```

