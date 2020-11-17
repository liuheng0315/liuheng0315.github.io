---
title: leetcode-1249. 移除无效的括号
date: 2020-10-10 16:18:39
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1249. 移除无效的括号](https://leetcode-cn.com/problems/minimum-remove-to-make-valid-parentheses/)

**原始思路**

```java
class Solution {
    public String minRemoveToMakeValid(String s) {
        Stack<Integer> stack = new Stack<>();
        char ch1 = '(';
        char ch2 = ')';
        Set<Integer> set = new HashSet<>();
        for (int i = 0; i < s.length(); i++) {
            // 将'('入栈
            char ch = s.charAt(i);
            if (ch == ch1) {
                stack.push(i);
            } else if (ch == ch2) {
                if (!stack.empty()) {
                    stack.pop();
                }else {
                    //如果栈中没有元素,则记录需要删除的idx
                    set.add(i);
                }
            }
        }

        //将栈中剩余的与set的元素都删除
        String ans = "";
        for (int i = 0; i < s.length(); i++) {
            if (!stack.contains(i) && !set.contains(i)) {
                ans += s.charAt(i);
            }
        }
        return ans;
    }
}
```

