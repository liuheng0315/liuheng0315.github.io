---
title: leetcode-71. 简化路径
date: 2021-03-26 13:44:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [71. 简化路径](https://leetcode-cn.com/problems/simplify-path/)

```java
class Solution {
    public String simplifyPath(String path) {
        Stack<String> stack = new Stack<>();

        String[] arr = path.split("/");
        for(String s : arr){
            if (s.equals("")) {
                continue;
            }

            if (s.equals(".")) {
                continue;
            }
            if (s.equals("..")) {
                if (!stack.isEmpty()){
                    stack.pop();
                }
                continue;
            }
            stack.add(s);
        }

        Stack<String> stack2 = new Stack<>();
        while (!stack.isEmpty()) {
            stack2.add(stack.pop());
        }

        String ans = "";
        while (!stack2.isEmpty()) {
            ans += "/" + stack2.pop();
        }
        return ans == "" ? "/" : ans;
    }
}

```

