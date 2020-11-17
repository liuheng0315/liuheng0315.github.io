---
title: leetcode-941. 有效的山脉数组
date: 2020-11-03 09:26:04
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-941. 有效的山脉数组](https://leetcode-cn.com/problems/valid-mountain-array/)

```java
class Solution {
    public boolean validMountainArray(int[] A) {
        // 确定波峰位置
        int n = A.length;
        if(n < 3){
            return false;
        }
        for(int i = 1; i < n-1; i++){
            //判断左边是全上升,右边是全下降
            int k1 = i;
            int k2 = i;
            while(k1 >= 1 && A[k1] > A[k1-1]){
                k1--;
            }

            if(k1 != 0){
                continue;
            }

            while(k2 < n-1 && A[k2] > A[k2+1]){
                k2++;
            }

            if(k2 == n -1){
                return true;
            }
        }
        return false;
    }
}
```

