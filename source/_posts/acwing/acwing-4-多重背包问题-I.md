---
title: acwing-4. 多重背包问题 I
date: 2020-09-20 23:19:02
categories: 
		- algorithm
tags: 
	- 动态规划
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-4. 多重背包问题 I](https://www.acwing.com/problem/content/4/)

```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int V = sc.nextInt();
        int[] w = new int[N + 1];
        int[] value = new int[N + 1];
        int[] nums = new int[N+1];
        for(int i = 1; i<= N; i++){
            w[i] = sc.nextInt();
            value[i] = sc.nextInt();
            nums[i] = sc.nextInt();
        }
        int res = getMaxValue(N, w, value, V, nums);
        System.out.print(res);
    }
    
    public static int getMaxValue(int n, int[] w, int[] value, int V, int[] nums){
        // dp中存的是最大价值
        int[][] dp = new int[n + 1][V + 1];
        
        for(int i = 1; i <= n; i++){
            for(int j = 1; j <= V; j++){
                for(int k = 0; k*w[i] <= j && k <= nums[i]; k++){
                   dp[i][j] = Math.max(dp[i][j], dp[i-1][j-k*w[i]] + k*value[i]);
                }
            }
        }
        return dp[n][V];
    }
}
```

