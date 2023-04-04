---
title: leetcode-1720. 解码异或后的数组
date: 2021-05-06 16:19:36
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1720. 解码异或后的数组](https://leetcode-cn.com/problems/decode-xored-array/)

```java
class Solution {
    public int[] decode(int[] encoded, int first) {
        int n = encoded.length;
        int[] arr = new int[n+1];
        arr[0] = first;
        for(int i = 0; i < n; i++){
            int k = encoded[i];
            int j = arr[i];
            int ans = 0;
            int cnt = 0;
            while(k!=0){
                int x;
                int a = (k & 1);
                int b = (j & 1);
                if(a == 1){
                    if(b == 0){
                        x = 1;
                    }else{
                        x = 0;
                    }
                }else{
                    if(b == 0){
                        x = 0;
                    }else{
                        x = 1;
                    }
                }
                ans = ans | x << cnt;
                cnt++;
                k = k >> 1;
                j = j >> 1;
            }
            if(j != 0){
                ans = ans | (j << cnt);
            }
            arr[i+1] = ans;
        }
        return arr;
    }
}
```

