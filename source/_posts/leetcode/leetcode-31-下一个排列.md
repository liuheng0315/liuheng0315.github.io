---
title: leetcode-31. 下一个排列
date: 2020-11-12 12:47:41
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [31. 下一个排列](https://leetcode-cn.com/problems/next-permutation/)

```java
class Solution {
    public void nextPermutation(int[] nums) {
        int n = nums.length;
        if (n <= 1){
            return;
        }
        boolean b = true;
        for (int i = n-1; i >= 0 ; i--) {
            // 选出比nums[i]大的最小的index
            int k = -1;
            for (int j = i+1; j <= n - 1 ; j++){
                if (nums[j] > nums[i]){
                    if (k == -1 || nums[j] < nums[k]){
                        k = j;
                    }
                }
            }

            if (k != -1){
                int tmp = nums[k];
                nums[k] = nums[i];
                nums[i] = tmp;

                // 对于结果进行排序
                Arrays.sort(nums, i+1, n);
                return;
            }
        }

        // 进行倒序
        int i = 0;
        int j = n - 1;
        while (true){
            if (i >= j) {
                break;
            }

            int tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
            i++;
            j--;
        }
    }
}
```

