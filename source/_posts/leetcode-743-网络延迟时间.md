---
title: leetcode-743. 网络延迟时间
date: 2020-09-30 17:12:26
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-743. 网络延迟时间](https://leetcode-cn.com/problems/network-delay-time/)

**floyd算法求多源最短路**

```java
class Solution {
    int[][] dist;
    int inf = 1000000000;
    
    public int networkDelayTime(int[][] times, int N, int K) {
        dist = new int[N + 1][N + 1];
        for (int i = 0; i < N + 1; i++) {
            for (int j = 0; j < N + 1; j++) {
                if (i == j) {
                    // 自己到自己距离为0
                    dist[i][j] = 0;
                } else {
                    dist[i][j] = inf;
                }
            }
        }

        // 读入数据
        for (int[] arr : times) {
            int a = arr[0];
            int b = arr[1];
            int c = arr[2];
            dist[a][b] = Math.min(dist[a][b], c);
        }

        floyd(N);

        int result = 0;
        for (int i = 1; i <= N; i++) {
            if (i == K) {
                continue;
            }
            result = Math.max(result, dist[K][i]);
        }
        return result > inf/2 ? -1 : result;
    }

    public void floyd(int N){
        for (int k = 1; k <= N; k++){
            for (int i = 1; i <= N; i++) {
                for (int j = 1; j <= N; j++) {
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
}
```

