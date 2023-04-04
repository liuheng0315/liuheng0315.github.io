---
title: leetcode-1893. 检查是否区域内所有整数都被覆盖
date: 2021-07-23 10:19:57
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1893. 检查是否区域内所有整数都被覆盖](https://leetcode-cn.com/problems/check-if-all-the-integers-in-a-range-are-covered/)

```java
class Solution {
    public boolean isCovered(int[][] ranges, int left, int right) {
        for(int[] arr : ranges){
            int l = arr[0];
            int r = arr[1];
            if(left >= l && left<= r){
                if(r >= right){
                    return true;
                }
                if(isCovered(ranges, r+1, right)){
                    return true;
                }
            }
        }
        return false;
    }
}
```

