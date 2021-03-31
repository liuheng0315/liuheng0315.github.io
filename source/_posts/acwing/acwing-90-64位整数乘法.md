---
title: acwing-90. 64位整数乘法
date: 2021-03-31 17:15:12
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

[acwing-90. 64位整数乘法](https://www.acwing.com/problem/content/92/)

```java
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        long a = sc.nextLong();
        long b = sc.nextLong();
        long p = sc.nextLong();
        System.out.println(multiply(a, b, p));
    }
    
    public static long multiply(long a, long b, long p){
        long ans = 0 % p;
        while(b > 0){
            if((b&1) == 1){
                ans = (ans + a) % p;
            }
            a = a * 2 % p;
            b = b >> 1;
        }
        return ans;
    }
}
```

