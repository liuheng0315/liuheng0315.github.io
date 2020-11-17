---
title: leetcode-1431. 拥有最多糖果的孩子
date: 2020-10-14 18:22:50
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1431. 拥有最多糖果的孩子](https://leetcode-cn.com/problems/kids-with-the-greatest-number-of-candies/)

```java
class Solution {
    public List<Boolean> kidsWithCandies(int[] candies, int extraCandies) {
        int max = Integer.MIN_VALUE;
        int n = candies.length;
        for (int i = 0; i < n; i++) {
            max = Math.max(max, candies[i]);
        }

        List<Boolean> list = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (candies[i] + extraCandies >= max) {
                list.add(true);
            }else {
                list.add(false);
            }
        }
        return list;
    }
}
```

