---
title: leetcode-1624. 两个相同字符之间的最长子字符串
date: 2020-10-19 12:21:58
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1624. 两个相同字符之间的最长子字符串](https://leetcode-cn.com/problems/largest-substring-between-two-equal-characters/)

```java
class Solution {
    public int maxLengthBetweenEqualCharacters(String s) {
        int n = s.length();
        int max = -1;
        for (int i = 0; i < n; i++) {
            for (int j = n - 1; j > i; j--) {
                if (s.charAt(i) == s.charAt(j)){
                    max = Math.max(max, j - i - 1);
                }
            }
        }
        return max;
    }
}
```

