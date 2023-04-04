---
title: leetcode-1310. 子数组异或查询
date: 2021-05-12 09:07:57
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1310. 子数组异或查询](https://leetcode-cn.com/problems/xor-queries-of-a-subarray/)

```java
class Solution {
    public int[] xorQueries(int[] arr, int[][] queries) {
        int n = arr.length;
        int[] s = new int[n+1];
        for(int i = 1; i <= n; i++){
            s[i] = s[i-1] ^ arr[i-1];
        }
        int[] ans = new int[queries.length];
        for(int i = 0; i < queries.length ; i++){
            int[] tmp = queries[i];
            int a = tmp[0];
            int b = tmp[1];
            ans[i] = s[b+1] ^ s[a];
        }
        return ans;
    }
}
```

