---
title: leetcode-171. Excel 表列序号
date: 2023-04-04 23:29:08
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

[171. Excel 表列序号](https://leetcode.cn/problems/excel-sheet-column-number/description/)

```java
import java.math.*;
class Solution {
    public int titleToNumber(String columnTitle) {
        if(columnTitle == null){
            return 0;
        }

        int n = columnTitle.length();
        int res = 0;

        // 大A的为65
        for(int i = 0; i < columnTitle.length(); i++){
            char x = columnTitle.charAt(i);
            int k = Integer.valueOf(x) - Integer.valueOf('A') + 1 ;
            res += Math.pow(26, n - i - 1) * k;
        }

        return res;
    }
}
```

