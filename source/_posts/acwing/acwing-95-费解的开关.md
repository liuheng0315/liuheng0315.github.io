---
title: acwing-95. 费解的开关
date: 2021-04-12 20:25:51
categories: 
		- algorithm
tags: 
	- 位运算
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-95. 费解的开关](https://www.acwing.com/problem/content/97/)

```java
import java.util.*;
public class Main{
    static int[][] arr = new int[5][5];
    static int[][] backup = new int[5][5];
    public static void  main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        for(int i = 0; i < n; i++){
            for(int k = 0; k < 5; k++){
                String str = sc.next();
                for(int j = 0; j < str.length(); j++){
                    arr[k][j] = Integer.valueOf(str.charAt(j)+"");
                }
            }

            // 方法调用
            System.out.println(work(arr));
        }
    }
    
    public static int work(int[][] arr){
        int ans = Integer.MAX_VALUE >> 1;
        
        // 遍历第一行的25种状态
        for(int i = 0; i < 1 << 5; i ++){
            
            //对数组进行备份
            for(int k = 0; k < 5; k ++){
                for(int q =0; q < 5; q++){
                    backup[k][q] = arr[k][q];
                }
            }

            int res = 0;
            // 遍历第一行开关
            for(int j = 0; j < 5; j++){
                // 第j位为1代表我们按下了开关
                if((i >> j & 1) == 1){
                    res++;
                    turn(backup, 0, j);
                }
            }
            
            // 遍历剩下的四行开关
            for(int k = 0; k < 4; k++){
                for(int j = 0; j < 5; j++){
                    if(backup[k][j] == 0){
                        res++;
                        turn(backup, k+1, j);
                    }
                }
            }
            
            //遍历最后一个行,看是否已全为1
            boolean bool = true;
            for(int j = 0; j < 5; j++){
                if(backup[4][j] == 0){
                    bool = false;
                }
            }
            
            if(bool){
                ans = Math.min(ans, res);
            }
        }
        return ans > 6 ? -1 : ans;
    }
    
    static int[] dx = new int[]{0, -1, 0, 1, 0};
    static int[] dy = new int[]{0, 0, 1, 0, -1};
    public static void turn(int[][] arr, int x, int y){
        for(int k = 0; k < 5; k++){
            int a = x + dx[k];
            int b = y + dy[k];
            if(a >= 0 && a < 5 && b >= 0 && b < 5){
                arr[a][b] = arr[a][b] == 0 ? 1 : 0;
            }
        }
    }
}
```

