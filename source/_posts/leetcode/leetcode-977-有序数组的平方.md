---
title: leetcode-977. 有序数组的平方
date: 2020-10-16 10:50:00
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-977. 有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)

```java
class Solution {
    public int[] sortedSquares(int[] A) {
        int n = A.length;
        for(int i = 0; i < n; i++){
            A[i] = A[i]*A[i];
        }
        Arrays.sort(A);
        return A;
    }
}
```

