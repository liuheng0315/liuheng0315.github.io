---
title: leetcode-1567.乘积为正数的最长子数组长度
date: 2020-09-04 17:23:36
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-1567.乘积为正数的最长子数组长度](https://leetcode-cn.com/problems/maximum-length-of-subarray-with-positive-product/)

**原始思路**

```java
class Solution {
    public int getMaxLen(int[] nums) {
        return findMax(nums);
    }
    public static int findMax(int[] nums){
        //从第一个位置开始,遇到0了就停止并返回最大的数组乘积的起始与结束位置的index
        Integer max;
        // 记录此时max为正数的
        Integer endIndex;
        //初始化最大长度为0
        int maxLen = 0;

        for (int i =0; i <nums.length; i++){
            endIndex = -1;
            max = null;

            //剪枝
            if ((nums.length - i + 1)<=maxLen){
                return maxLen;
            }

            //记录出现负数的次数
            for (int j =i; j <nums.length; j++){
                if (nums[j] == 0){
                    //结束循环
                    break;
                }
                int num = nums[j] > 0 ? 1 : -1;
                if (max == null) {
                    max = num;
                }else {
                    max = max * num;
                }
                if (max > 0) {
                    endIndex = j;
                }
            }

            //记录此时为正数的最大长度
            if (endIndex - i + 1 > maxLen){
                maxLen = endIndex - i + 1;
            }
        }

        return maxLen;
    }
}
```
