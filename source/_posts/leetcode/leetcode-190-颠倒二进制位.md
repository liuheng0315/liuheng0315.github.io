---
title: leetcode-190. 颠倒二进制位
date: 2021-03-31 11:47:37
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [190. 颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)

```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int ans = 0;
        for (int i = 0; i < 32; i++) {
            ans = ans << 1;
            ans = (n&1) | ans;
            n = n >> 1;
        }
        return ans;
    }
}
```

