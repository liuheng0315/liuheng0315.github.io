---
title: leetcode-2670找出不同元素数目差数组
date: 2024-02-01 00:11:09
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

[leetcode-2670找出不同元素数目差数组](https://leetcode.cn/problems/find-the-distinct-difference-array/description/?envType=daily-question&envId=2024-01-31)
初始解题思路:

```java
class Solution {
    public int[] distinctDifferenceArray(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        int[][] dp = new int[n][n];
        Set<Integer> set1 = new HashSet<Integer>();
        Set<Integer> set2 = new HashSet<Integer>();

        for (int i = 0; i < n; i++) {
            set1.add(nums[i]);
            set2.clear();
            for (int j = i + 1; j < n; j++) {
                set2.add(nums[j]);
            }
            res[i] = set1.size() - set2.size();
        }

        return res;
    }
}
```



官方题解：

```java
class Solution {
    public int[] distinctDifferenceArray(int[] nums) {
        int n = nums.length;
        Set<Integer> set = new HashSet<Integer>();
        int[] sufCnt = new int[n + 1];
        for (int i = n - 1; i > 0; i--) {
            set.add(nums[i]);
            sufCnt[i] = set.size();
        }
        int[] res = new int[n];
        set.clear();
        for (int i = 0; i < n; i++) {
            set.add(nums[i]);
            res[i] = set.size() - sufCnt[i + 1];
        }
        return res;
    }
}
```

