---
title: acwing-二分查找
date: 2020-09-09 22:05:44
categories: 
		- 算法
tags: 
	- 二分查找
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-二分查找](https://www.acwing.com/problem/content/description/791/)

```java
class Solution{
    public int binarySearch(int[] arr, int k){
        //定义指针
        int left = 0;
        int right = arr.length - 1;
        middle = 0;
        
        while(left < right){
            middle = (left + right)/2;
            if(arr[middle] < k){
                right = middle - 1;
            }else if(arr[middle] > k){
                left = middle + 1;
            }else{
               	return arr[middle];
            }
        }
        //表示没有找到
        return -1;
    }
}
```

