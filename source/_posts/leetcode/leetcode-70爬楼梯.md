---
title: leetcode-70.爬楼梯
date: 2020-09-11 15:54:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-70.爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

**原始思路**

```java
class Solution {
    public int climbStairs(int n) {
        if (n == 0 || n == 1){
            return n;
        }
        // 定义初始状态
        int[] mem = new int[n+1];
        mem[1] = 1;
        mem[2] = 2;
        for (int i = 3; i<= n; i++){
            mem[i] = mem[i-1] + mem[i-2];
        }
        return mem[n];
    }
}
```

 