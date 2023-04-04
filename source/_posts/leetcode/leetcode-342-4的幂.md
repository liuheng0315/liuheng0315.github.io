---
title: leetcode-342. 4的幂
date: 2021-05-31 10:38:36
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [342. 4的幂](https://leetcode-cn.com/problems/power-of-four/)

```java
class Solution {
    public boolean isPowerOfFour(int n) {
        if(n <= 0){
            return false;
        }

        if(n == 1){
            return true;
        }

        // 位运算
        int cnt = 0;
        while(n != 0){
            if((n&1) != 0){
                int tmp = n >> 1;
                if(tmp == 0 && cnt % 2 == 0){
                    return true;
                }else{
                    return false;
                }
            }
            n = n >> 1;
            cnt++;
        }
        return false;
    }
}
```

