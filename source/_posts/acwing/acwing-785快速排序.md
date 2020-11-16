---
title: acwing-785.快速排序
date: 2020-09-07 22:53:34
categories: 
		- 算法
tags: 
	- 排序算法
cover: images/leetcode.jpg
typora-root-url: ..
---

[acwing-785.快速排序](https://www.acwing.com/problem/content/description/787/)

```java
import java.io.*;
import java.lang.*;
Class Main{
    //快排
    public static void quickSort(int[] arr, int left, int right){
        if(left >= right){
            return;
        }
        int middle = arr[left + (right - left)/2];
        // -1 和 +1 是为了扩一位
        int l = left - 1;
        int r = right + 1;
        while(l < r){
            do{
                l++;
            }while(arr[l] < middle);
            
            do{
                r--;
            }while(arr[r] > middle);
            
            if(l < r){
                int tmp = arr[l];
                arr[l] = arr[r];
                arr[r] = tmp;
            }
        }
        quickSort(arr, left ,r);
        quickSort(arr, r+1, right);
    }
    
    public static void main(String[] args){
        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
        try{
            String n = bf.readLine();
            String str = bf.readLine();
            int[] arr = new int[Integer.valueOf(n)];
            String[] arrStr = str.split(" ");
            for(int i = 0; i < arrStr.length; i++){
                arr[i] = Integer.valueOf(arrStr[i]);
            }

            quickSort(arr, 0, arr.length - 1);

            for(int i = 0; i < arr.length; i++){
                System.out.print(arr[i]);
                if(i != arr.length -1){
                    System.out.print(" ");
                }
            }
        }catch(Exception e){
            System.out.println(e);
        }
    }
}
```

