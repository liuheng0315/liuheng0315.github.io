---
title: leetcode-152.乘积最大子数组
date: 2020-09-14 16:57:00
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

**原始思路**

```java
class Solution {
    public int maxProduct(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n][2];
        
        //初始化状态值dp[i][0]代表最小值,dp[i][1]代表最大值
        dp[0][0] = nums[0];
        dp[0][1] = nums[0];
        
        //从第二个数开始遍历
        for(int i = 1; i < n; i++){
            if(nums[i] <= 0){
                //小于0时,如果最小值为负数,此时相乘为最大值
                dp[i][0] = Math.min(nums[i], dp[i-1][1]*nums[i]);
                dp[i][1] = Math.max(nums[i], dp[i-1][0]*nums[i]);
            }else{
                // 大于0时
                dp[i][0] = Math.min(nums[i], dp[i-1][0]*nums[i]);
                dp[i][1] = Math.max(nums[i], dp[i-1][1]*nums[i]);
            }
        }

        //遍历整个dp找到最大值
        int res = dp[0][1];
        for(int i = 1; i < n; i++){
            if(dp[i][1] > res){
                res = dp[i][1];
            }
        }
        return res;
    }
}
```





**题解思路--使用滑动变量缩小空间**

```java
class Solution {
    public int maxProduct(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n][2];
        
        //初始化状态值dp[i][0]代表最小值,dp[i][1]代表最大值
        dp[0][0] = nums[0];
        dp[0][1] = nums[0];
        
        int max = nums[0];
        int min = nums[0];
        //用一个一维的数组记录最大乘积
        int[] arr = new int[n];

        //从第二个数开始遍历
        for(int i = 1; i < n; i++){
            if(nums[i] <= 0){
                //小于0时,如果最小值为负数,此时相乘为最大值
                dp[i][0] = Math.min(nums[i], dp[i-1][1]*nums[i]);
                dp[i][1] = Math.max(nums[i], dp[i-1][0]*nums[i]);
            }else{
                // 大于0时
                dp[i][0] = Math.min(nums[i], dp[i-1][0]*nums[i]);
                dp[i][1] = Math.max(nums[i], dp[i-1][1]*nums[i]);
            }
        }

        //遍历整个dp找到最大值
        int res = dp[0][1];
        for(int i = 1; i < n; i++){
            if(dp[i][1] > res){
                res = dp[i][1];
            }
        }
        return res;
    }
}
```

