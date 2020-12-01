---
title: leetcode-1124. 表现良好的最长时间段
date: 2020-12-01 20:43:35
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-1124. 表现良好的最长时间段](https://leetcode-cn.com/problems/longest-well-performing-interval/)

```java
class Solution {
    public int longestWPI(int[] hours) {
        int n = hours.length;
        int ans = 0;
        for (int i = 0; i < n; i++) {
            int count = 0;
            for (int j = i; j < n; j++) {
                if (hours[j] > 8){
                    count = count + 1;
                }else {
                    count = count - 1;
                }

                if (count > 0){
                    ans = Math.max(ans, j-i+1);
                }
            }
        }

        return ans;
    }
}
```

