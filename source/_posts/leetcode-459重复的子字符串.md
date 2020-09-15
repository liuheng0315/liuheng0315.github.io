---
title: leetcode-459.重复的子字符串
date: 2020-09-11 12:34:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-459.重复的子字符串](https://leetcode-cn.com/problems/repeated-substring-pattern/)

```java
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        int n = s.length();
        //暴力法,假设重复的个数是1到n
        for (int i = 1; i*2 <= n; i++) {
            if (n % i == 0){
                Boolean bool = true;
                for (int j = i; j < n; j++) {
                    if (s.charAt(j - i) != s.charAt(j)) {
                        bool = false;
                        break;
                    }
                }
                if (bool) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

