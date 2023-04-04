---
title: leetcode-1734. 解码异或后的排列
date: 2021-05-12 09:34:50
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1734. 解码异或后的排列](https://leetcode-cn.com/problems/decode-xored-permutation/)

```java
class Solution {
    public int[] decode(int[] encoded) {
        int n = encoded.length;
        int[] arr = new int[n+1];
        int total = 0;
        for(int i = 1; i <= n+1; i ++){
            total ^= i;
        }

        int tmp = 0;
        for(int i = 1; i < n; i = i + 2){
            tmp ^= encoded[i];
        }

        arr[0] = total ^ tmp;
        for(int i = 1; i <= n; i++){
            arr[i] = arr[i-1] ^ encoded[i-1];
        }
        return arr;
    }
}
```

