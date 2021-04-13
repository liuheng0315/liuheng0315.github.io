---
title: acwing-787.归并排序
date: 2020-09-08 23:43:02
categories: 
		- algorithm
tags: 
	- 排序算法
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-787.归并排序](https://www.acwing.com/problem/content/789/)

```java
import java.util.*;
class Main{
    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int[] arr = new int[n];
        for(int i = 0; i < n; i++){
            arr[i] = in.nextInt();
        }
        
        //进行排序
        sort(arr, 0, arr.length - 1);
        
        for(int i = 0; i < n; i++){
            System.out.print(arr[i] + " ");
        }
    }
    
    public static void sort(int[] arr, int left, int right){
        int middle = left + right >> 1;
        if(left >= right){
            return;
        }
        //左右分别排序
        sort(arr, left, middle);
        sort(arr, middle + 1, right);
        
        //进行归并,这一步是归并排序中的难点
        //申请一个right - left + 1大小的数组作为临时数组
        int[] tmp = new int[right - left + 1];
        //定义两个指针和一个数组指针
        int k = 0;
        int l = left;
        int r = middle + 1;
        //循环将两个数组按数据放入tmp中
        while(l <= middle && r <= right){
            if(arr[l] <= arr[r]){
                tmp[k++] = arr[l++];
            }else{
                tmp[k++] = arr[r++];
            }
        }
        //如果左右数组中的某一个到达末尾,就把剩余的一个数组加到后面
        while(l <= middle){
            tmp[k++] = arr[l++];
        }
        while(r <= right){
            tmp[k++] = arr[r++];
        }
        
        //将临时数组的值复制到原数组中
        for(int i = left , j = 0; i <= right; i++,j++){
            arr[i] = tmp[j];
        }
    }
}
```

