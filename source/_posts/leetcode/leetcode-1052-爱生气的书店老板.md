---
title: leetcode-1052. 爱生气的书店老板
date: 2020-11-30 20:18:10
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..	
---

#### [leetcode-1052. 爱生气的书店老板](https://leetcode-cn.com/problems/grumpy-bookstore-owner/)

```java
class Solution {
    public int maxSatisfied(int[] customers, int[] grumpy, int X) {
        int ans = 0;
        int k = 0;
        int n = customers.length;

        int i = 0;
        int j = 0;
        int grum = 0;
        while(j < n){
            if(grumpy[j] == 1){
                grum += customers[j];
            }else{
                ans += customers[j];
            }
            
            j++;
            while(j - i == X){
                k = Math.max(grum, k);
                if(grumpy[i] == 1){
                    grum -= customers[i];
                }
                i++;
            }
        }

        
        return ans + k;
    }
}
```

