---
title: leetcode-面试题 10.01. 合并排序的数组
date: 2021-02-05 11:22:08
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [面试题 10.01. 合并排序的数组](https://leetcode-cn.com/problems/sorted-merge-lcci/)

```java
class Solution {
    public void merge(int[] A, int m, int[] B, int n) {
        int len = m + n;
        for (int i = len - 1; i >= 0; i--) {
            if (m == 0){
                // 把B放到A
                for (int j = 0; j < n; j++) {
                    A[j] = B[j];
                }
                return;
            }

            if (n == 0){
                // 直接return
                return;
            }

            if (A[m-1] > B[n-1]){
                A[i] = A[m-1];
                m --;
            }else {
                A[i] = B[n-1];
                n--;
            }
        }
    }
}
```

