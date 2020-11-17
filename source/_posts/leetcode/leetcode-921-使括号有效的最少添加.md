---
title: leetcode-921. 使括号有效的最少添加
date: 2020-09-30 13:02:48
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-921. 使括号有效的最少添加](https://leetcode-cn.com/problems/minimum-add-to-make-parentheses-valid/)

**原始思路**

```java
class Solution {
    public int minAddToMakeValid(String S) {
        char c1 = '(';
        char c2 = ')';
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < S.length(); i++) {
            char chr = S.charAt(i);
            if (stack.empty()) {
                stack.push(chr);
            }else {
                if (chr == c2 && stack.peek() == c1 ) {
                    stack.pop();
                }else {
                    stack.push(chr);
                }
            }
        }
        return stack.size();
    }
}
```

