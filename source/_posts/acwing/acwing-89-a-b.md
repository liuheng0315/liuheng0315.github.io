---
title: acwing-89. a^b
date: 2021-03-30 23:27:56
categories: 
		- algorithm
tags: 
	- 位运算
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-89. a^b](https://www.acwing.com/problem/content/91/)

```java
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int a = sc.nextInt();
        int b = sc.nextInt();
        int p = sc.nextInt();
        
        System.out.println(power(a, b, p));
    }
    
    public static long power(long a, long b, long p){
        long ans = 1 % p;
        while(b > 0){
            if((b&1) == 1){
                ans = (ans * a) % p;
            }
            
            a = (a * a) % p;
            b = b >> 1;
        }
        
        return ans;
    }
}
```

