---
title: leetcode-面试题 08.09. 括号
date: 2020-10-12 12:53:50
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-面试题 08.09. 括号](https://leetcode-cn.com/problems/bracket-lcci/)

```java
class Solution {
    List<String> result = new ArrayList<>();
    public List<String> generateParenthesis(int n) {
        dfs(0, 0, "", n);
        return result;
    }

    public void dfs(int left, int right, String str, int n) {
        if (str.length() == 2 * n) {
            result.add(str);
        }
        if (left < n) {
            dfs(left + 1, right, str + "(", n);
        }

        if (right < left && right < n) {
            dfs(left, right + 1, str + ")", n);
        }
    }
}
```

