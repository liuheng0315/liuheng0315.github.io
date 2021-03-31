---
title: leetcode-1190. 反转每对括号间的子串
date: 2021-03-26 13:45:54
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1190. 反转每对括号间的子串](https://leetcode-cn.com/problems/reverse-substrings-between-each-pair-of-parentheses/)

```java
class Solution {
    public String reverseParentheses(String s) {
        StringBuffer sb = new StringBuffer();
        Stack<Character> stack = new Stack<>();
        int count = 0;
        boolean bool = true;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                bool = true;
                count++;
            } else if (s.charAt(i) == ')') {
                bool = false;
                count--;
            }

            if (count == 0 && s.charAt(i) != ')') {
                sb.append(s.charAt(i));
            }else {
                if (bool) {
                    // 入栈
                    stack.add(s.charAt(i));
                }else {
                    // 将最近的'('上的字符出栈
                    String str = "";
                    while (!stack.isEmpty() && stack.peek() != '(') {
                        str = str + stack.pop();
                    }
                    if (!stack.isEmpty()){
                        stack.pop();
                    }

                    if (stack.isEmpty()) {
                        sb.append(str);
                    }else {
                        for (int j = 0; j < str.length(); j++) {
                            stack.add(str.charAt(j));
                        }
                    }
                }
                bool = true;
            }
        }
        return sb.toString();
    }
}
```

