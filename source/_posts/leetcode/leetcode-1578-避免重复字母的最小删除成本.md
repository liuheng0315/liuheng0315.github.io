---
title: leetcode-1578. 避免重复字母的最小删除成本
date: 2020-09-22 13:35:35
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1578. 避免重复字母的最小删除成本](https://leetcode-cn.com/problems/minimum-deletion-cost-to-avoid-repeating-letters/)

**原始思路**

```java
class Solution {
    public int minCost(String s, int[] cost) {
        int result = 0;
        int n = s.length();
        for (int i = 1; i < n; i++) {
            int j = i;
            while (j < n && s.charAt(j) == s.charAt(j - 1) ) {
                j++;
            }
            int total = 0;
            int max = 0;
            for (int k = i-1; k <= j-1; k++) {
                total += cost[k];
                max = Math.max(max, cost[k]);
            }
            result += (total - max);
            i = j;
        }
        return result;
    }
}
```



**题解思路**

```java
class Solution {
    public int minCost(String s, int[] cost) {
        int result = 0;
        int n = s.length();
        for (int i = 1; i < n; i++) {
            if (s.charAt(i) == s.charAt(i - 1) ) {
                result += Math.min(cost[i],cost[i-1]);
                // 交换位置
                if(cost[i-1] > cost[i]){
                    int tmp =cost[i];
                    cost[i] = cost[i-1];
                    cost[i-1] = tmp;
                }
            }
        }
        return result;
    }
}
```

