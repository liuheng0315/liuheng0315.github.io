---
title: leetcode-852. 山脉数组的峰顶索引
date: 2021-06-16 19:30:34
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [852. 山脉数组的峰顶索引](https://leetcode-cn.com/problems/peak-index-in-a-mountain-array/)

```java
class Solution {
    public int peakIndexInMountainArray(int[] arr) {
        // 二分查找
        int i = 0;
        int j = arr.length - 1;

        while(true){
            int mid = (i + j)/2;
            if(arr[mid] > arr[mid-1] && arr[mid] > arr[mid+1]){
                return mid;
            }else if(arr[mid]>arr[mid-1]){
                i = mid;
            }else if(arr[mid]<arr[mid-1]){
                j = mid;
            }
        }
    }
}
```

