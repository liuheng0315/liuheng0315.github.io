---
title: leetcode-1011. 在 D 天内送达包裹的能力
date: 2021-04-26 09:39:40
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1011. 在 D 天内送达包裹的能力](https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days/)

```java
class Solution {
    public int shipWithinDays(int[] weights, int D) {
        return binarySearch(0, Integer.MAX_VALUE, weights, D);
    }

    public int binarySearch(int l, int r, int[] weights, int D){
        while(l < r){
            int mid = l + (r-l)/2;
            if(isValid(mid, weights, D)){
                r = mid;
            }else{
                l = mid + 1;
            }
        }
        return l;
    }

    public boolean isValid(int ans, int[] weights, int D){
        int day = 0;
        int w2 = ans+1;
        for(int w : weights){
            if(ans - w2 >= w){
                w2+=w;
            }else if(w <= ans){
                w2 = w;
                day ++;
            }else{
                return false;
            }
        }
        if(day <= D){
            return true;
        }
        return false;
    }
}
```

