---
title: leetcode-1529. 灯泡开关 IV
date: 2020-10-16 13:00:16
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1529. 灯泡开关 IV](https://leetcode-cn.com/problems/bulb-switcher-iv/)

**原始思路**

```java
class Solution {
    public int minFlips(String target) {
        int count = 0;
        int n = target.length();
        char[] chars = new char[n];
        Arrays.fill(chars,'0');
        for (int i = 0; i < n; i++) {
            char ch = chars[i];
            if ((count & 1) == 1) {
                if (ch == '0'){
                    ch = '1';
                }else {
                    ch = '0';
                }
            }

            if (ch != target.charAt(i)) {
                // 需要反转
                count++;
            }
        }
        return count;
    }
}
```

