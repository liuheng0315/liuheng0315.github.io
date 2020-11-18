---
title: leetcode-134. 加油站
date: 2020-11-18 18:45:04
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-134. 加油站](https://leetcode-cn.com/problems/gas-station/)

```java
class Solution {
    int n = 0;
    int pre = 0;
    public int canCompleteCircuit(int[] gas, int[] cost) {
        n = gas.length;
        for (int i = 0; i < n; i++) {
            pre = i;
            if (dfs(gas, cost, i, gas[i])) {
                return i;
            }
        }
        return -1;
    }

    public boolean dfs(int[] gas, int[] cost, int cur , int totalGas) {
        if (totalGas < cost[cur]) {
            return false;
        }

        if ((cur+1) % n == pre && totalGas >= cost[cur]){
            return true;
        }

        for (int i = cur; i < n; i++) {
            int idx = (cur + 1) % n;
            return dfs(gas, cost, (i+1) % n, totalGas - cost[cur] + gas[idx]);
        }
        return false;
    }
}
```

