---
title: acwing- 854. Floyd求最短路
date: 2020-09-29 00:01:27
categories: 
		- 算法
tags: 
	- 图论
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[AcWing 854. Floyd求最短路](https://www.acwing.com/activity/content/code/content/48531/)

**基础模板**

```java
// 基础模板,伪代码
class Main{
    public static void main(String[] args){
        int inf = 1000000000;
        // n个点,定义邻接矩阵
        int[][] dist = new int[n+1][n+1];
        // 初始化邻接矩阵
        for(int i = 0; i < n; i++){
            for(int j = 0; j < n; i++){
                if(i==j){
                    // 自己到自己为0
                    dist[i][j] = 0;
                }else{
                    // 初始化无法达到的最大值
                    dist[i][j] = inf;
                }
            }
        }
        
        // 读入图数据 注意自环和重边
        int a = sc.nextInt();
        int b = sc.nextInt();
        int c = sc.nextInt();
        dist[a][b] = Math.min(dist[a][b], c)
        
        // 调用floyd算法
        floyd(dist, n);
    }

    public static void floyd(int[][] dist, int n){
        // 经过1到k这些点
        for(int k = 0; k < n; k++){
            for(int i = 0 ; i < n; i++){
                for(int j = 0; j < n; j++){
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
}
```



**题解思路:** [小呆呆题解](https://www.acwing.com/solution/content/6337/)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
    static int n;
    static int m;
    static int q;
    static int N = 210;
    static int INF = 0x3f3f3f3f;
    static int[][] d = new int[N][N];
    public static void floyd()
    {
        for(int k = 1;k <= n;k++)
            for(int i = 1;i <= n;i++)
                for(int j = 1;j <= n;j++)
                    d[i][j] = Math.min(d[i][j], d[i][k] + d[k][j]);
    }
    public static void main(String[] args) throws IOException {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        String[] str1 = reader.readLine().split(" ");
        n = Integer.parseInt(str1[0]);
        m = Integer.parseInt(str1[1]);
        q = Integer.parseInt(str1[2]);
        for(int i = 1;i <= n;i++)
        {
            for(int j = 1;j <= n;j++)
            {
                if(i == j) d[i][j] = 0;
                else d[i][j] = INF;
            }
        }
        while(m -- > 0)
        {
            String[] str2 = reader.readLine().split(" ");
            int a = Integer.parseInt(str2[0]);
            int b = Integer.parseInt(str2[1]);
            int c = Integer.parseInt(str2[2]);
            d[a][b] = Math.min(d[a][b], c);//若有重边选择短的边
        }
        floyd();
        while(q -- > 0)
        {
            String[] str3 = reader.readLine().split(" ");
            int a = Integer.parseInt(str3[0]);
            int b = Integer.parseInt(str3[1]);
            int t = d[a][b];
            if(t > INF / 2) System.out.println("impossible");
            else System.out.println(t);

        }
    }

}
```

