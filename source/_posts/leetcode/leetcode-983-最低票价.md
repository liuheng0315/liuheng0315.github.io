---
title: leetcode-983. 最低票价
date: 2020-10-14 15:30:12
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-983. 最低票价](https://leetcode-cn.com/problems/minimum-cost-for-tickets/)

**题解思路**

```java
class Solution {
    public int mincostTickets(int[] days, int[] costs) {
        int n = days.length;
        int last = days[n - 1];
        int[] dp = new int[last + 1];
        int idx = 0;
        for (int i = 1; i <= last; i++) {
            if (i == days[idx]) {
                int d1 = i - 1;
                int d2 = i - 7 < 0 ? 0 : i - 7;
                int d3 = i - 30 < 0 ? 0 : i - 30;
                dp[i] = Math.min(dp[d1] + costs[0],Math.min(dp[d2]+costs[1],dp[d3]+costs[2]));
                idx++;
            } else {
                dp[i] = dp[i - 1];
            }
        }
        return dp[last];
    }
}
```

