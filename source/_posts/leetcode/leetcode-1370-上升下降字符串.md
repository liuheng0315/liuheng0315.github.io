---
title: leetcode-1370. 上升下降字符串
date: 2020-11-25 09:59:04
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-1370. 上升下降字符串](https://leetcode-cn.com/problems/increasing-decreasing-string/)

```java
class Solution {
    public String sortString(String s) {
        char[] ch = s.toCharArray();
        Arrays.sort(ch);
        int n = ch.length;
        // 定义一个访问过的数组
        boolean[] st = new boolean[n];
        StringBuilder sb = new StringBuilder();

        while (sb.length() != n) {
            // 记录前一个字符
            char pre = 'A';
            for (int k = 0; k < n; k++) {
                if (!st[k] && ch[k] != pre){
                    sb.append(ch[k]);
                    st[k] = true;
                    pre = ch[k];
                }
            }
            pre = 'A';
            for (int k = n-1; k >= 0; k--) {
                if (!st[k] && ch[k] != pre){
                    sb.append(ch[k]);
                    st[k] = true;
                    pre = ch[k];
                }
            }
        }
        return sb.toString();
    }
}
```

