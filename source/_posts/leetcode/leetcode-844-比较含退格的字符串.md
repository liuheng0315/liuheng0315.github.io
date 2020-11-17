---
title: leetcode-844. 比较含退格的字符串
date: 2020-10-19 10:28:33
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-844. 比较含退格的字符串](https://leetcode-cn.com/problems/backspace-string-compare/)

```java
class Solution {
    public boolean backspaceCompare(String S, String T) {
        Stack<Character> stack1 = getStack(S);
        Stack<Character> stack2 = getStack(T);
        if (stack1.size() != stack2.size()) {
            return false;
        }

        while (!stack1.isEmpty()) {
            if (stack1.pop() != stack2.pop()) {
                return false;
            }
        }
        return true;
    }

    public Stack<Character> getStack(String S){
        Stack<Character> stack = new Stack<>();
        for (int i = 0; i < S.length(); i++) {
            if (S.charAt(i) == '#') {
                if (!stack.isEmpty()) {
                    stack.pop();
                }
            }else {
                stack.push(S.charAt(i));
            }
        }
        return stack;
    }
}
```

