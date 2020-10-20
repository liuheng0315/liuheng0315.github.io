---
title: acwing-282. 石子合并
date: 2020-10-20 23:26:47
categories: 
		- 算法
tags: 
	- 动态规划
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

 [acwing-282. 石子合并](https://www.acwing.com/problem/content/284/)

```java
import java.util.*;
class Main{
    static int[] arr;
    static int[] s;
    static int n ;
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        arr = new int[n];
        for(int i = 0; i < n; i++){
            arr[i] = sc.nextInt();
        }
        s = new int[n+1];
        System.out.println(getCost());
    }
    
    public static int getCost(){
        // 求前缀和
        for(int i = 1; i <= n; i++){
            s[i] = s[i-1] + arr[i-1];
        }
        
        // 初始化dp
        int[][] dp = new int[n+1][n+1];
        
        // 区间dp
        for(int l = 1; l < n; l++){
            for(int i = 1; i + l <= n; i++){
                int j = i + l;
                dp[i][j] = 100000000;
                for(int k = i; k < j; k++){
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k+1][j] + s[j] - s[i-1]);
                }
            }
        }
        
        return dp[1][n];
    }
}
```

