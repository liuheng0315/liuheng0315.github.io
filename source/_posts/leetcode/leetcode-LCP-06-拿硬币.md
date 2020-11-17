---
title: leetcode-LCP 06. 拿硬币
date: 2020-10-22 19:33:39
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-LCP 06. 拿硬币](https://leetcode-cn.com/problems/na-ying-bi/)

```java
class Solution {
    public int minCount(int[] coins) {
        int ans = 0;
        for (int n : coins) {
            int i = n / 2;
            int j = n % 2;
            ans = ans + i + j;
        }
        return ans;
    }
}
```

