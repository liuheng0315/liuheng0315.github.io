---
title: leetcode-1002. 查找常用字符
date: 2020-10-14 10:15:22
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1002. 查找常用字符](https://leetcode-cn.com/problems/find-common-characters/)

**题解思路**

```java
class Solution {
    public List<String> commonChars(String[] A) {
        List<String> result = new ArrayList<>();
        int n = A.length;
        int[] ans = new int[26];
        Arrays.fill(ans, 1000000000);

        for (String str : A) {
            int[] tmp = new int[26];
            for (int i = 0; i < str.length(); i++) {
                tmp[str.charAt(i) - 'a'] += 1;
            }

            for (int i = 0; i < 26; i++) {
                ans[i] = Math.min(ans[i], tmp[i]);
            }
        }

        for (int i = 0; i < 26; i++) {
            while (ans[i] != 0) {
                result.add(String.valueOf((char) ('a' + i)));
                ans[i] -= 1;
            }
        }

        return result;
    }
}
```

