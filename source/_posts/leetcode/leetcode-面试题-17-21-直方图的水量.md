---
title: leetcode-面试题 17.21. 直方图的水量
date: 2021-04-09 15:55:11
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [面试题 17.21. 直方图的水量](https://leetcode-cn.com/problems/volume-of-histogram-lcci/)

```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        int n = height.length;
        if(n == 0){
            return ans;
        }

        // 初始化左边最大
        int[] leftMax = new int[n];
        leftMax[0] = height[0];
        for(int i = 1; i < n; i++){
            leftMax[i] = Math.max(leftMax[i-1], height[i]);
        }

        // 初始化右边最大
        int[] rightMax = new int[n];
        rightMax[n-1] = height[n-1];
        for(int i = n - 2; i >= 0; i--){
            rightMax[i] = Math.max(rightMax[i+1], height[i]);
        }

        for(int i = 0; i < n; i++){
            ans = ans + Math.min(leftMax[i], rightMax[i]) - height[i];
        }
        return ans;
    }
}
```

