---
title: leetcode-55. 跳跃游戏
date: 2020-11-17 10:00:00
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

```java
class Solution {
    public boolean canJump(int[] nums) {
        int n = nums.length;
        int idx = nums[0];
        for(int i = 0; i <= idx; i++){
            idx = Math.max(idx, i + nums[i]);
            if (idx >= n - 1) {
                return true;
            }
        }
        return false;
    }
}
```

