---
title: leetcode-1566.重复至少 K 次且长度为 M 的模式
date: 2020-09-01 22:42:49
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

[leetcode-1566.重复至少 K 次且长度为 M 的模式](https://leetcode-cn.com/problems/detect-pattern-of-length-m-repeated-k-or-more-times/)

**原始思路实现**

```java
class Solution {
    public boolean containsPattern(int[] arr, int m, int k) {
        //边界判断
        if(arr.length < m || m<= 0){
            return false;
        }
        
        for(int i = 0; i + m -1 < arr.length; i++){
            //定义重复次数count
            int count = 1;
            //记录找到数组的最后角标
            int index = i + m -1;

            for(int j = index + 1 ; j+ m -1 <arr.length; j++){
                boolean b = true;
                //如何快速判断m个数组是相同的模式
                for(int x = 0; x < m; x++){
                    if(arr[i+x] != arr[j+x]){
                        b = false;
                    }
                }
                if(b){
                    count++;
                    //更新找到相同模板的数组的最后一位角标
                    index = j + m -1;
                }else{
                    //证明不连续了
                    break;
                }

                if(count == k){
                return true;
            }
                //后面的全部能匹配上也不够K时,没有必要在继续寻找,直接break
                if((arr.length-index)/m<(k-count)){
                    break;
                }
            }
        }
        return false;
    }
}
```



**观看题解后思路实现**

```java
class Solution {
    public boolean containsPattern(int[] arr, int m, int k) {
        //边界判断
        if(arr.length < m*k){
            return false;
        }
        
        for(int i = 0; i <= arr.length - m*k; i++){
            int j ;
            for(j = i+m; j < i + m*k; j++){
                if(arr[j] != arr[j-m]){
                    break;
                }
            }
            //如果此时存在j刚好到达i+k*m的位置,说明存在k个m的模板
            if(j == i + k*m){
                return true;
            }
        }
        return false;
    }
}
```

