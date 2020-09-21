---
title: leetcode-1593. 拆分字符串使唯一子字符串的数目最大
date: 2020-09-21 15:09:23
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1593. 拆分字符串使唯一子字符串的数目最大](https://leetcode-cn.com/problems/split-a-string-into-the-max-number-of-unique-substrings/)

**原始思路**

```java
class Solution {
    List<String> ans = new ArrayList<>();
    Set<String> set = new HashSet<>();
    int result = 0;
    public int maxUniqueSplit(String s) {
        dfs(0, s);
        return result;
    }

    public void dfs(int cur, String s) {
        result = Math.max(result, ans.size());

        for (int i = cur; i < s.length(); i++) {
            String str = s.substring(cur, i + 1);
            if (set.contains(str)) {
                continue;
            }
            ans.add(str);
            set.add(str);
            dfs(i + 1, s);
            ans.remove(ans.size() - 1);
            set.remove(str);
        }
    }
}
```

