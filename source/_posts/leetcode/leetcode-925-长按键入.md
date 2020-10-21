---
title: leetcode-925. 长按键入
date: 2020-10-21 09:41:23
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-925. 长按键入](https://leetcode-cn.com/problems/long-pressed-name/)

```java
class Solution {
    public boolean isLongPressedName(String name, String typed) {
        int j = 0;
        if (typed.length() < name.length()) {
            return false;
        }

        Character pre = null;
        for (int i = 0; i < name.length(); i++) {
            if (j >= typed.length()) {
                return false;
            }

            if (name.charAt(i) == typed.charAt(j)) {
                pre = name.charAt(i);
                j++;
                continue;
            }else {
                while ( j < typed.length() && pre != null && pre == typed.charAt(j)){
                    j++;
                }

                if (j >= typed.length()) {
                    return false;
                }

                if (name.charAt(i) != typed.charAt(j)){
                    return false;
                }else {
                    j++;
                    pre = name.charAt(i);
                }
            }
        }

        while (j < typed.length()) {
            if (typed.charAt(j) != pre) {
                return false;
            }
            j++;
        }
        return true;
    }
}
```

