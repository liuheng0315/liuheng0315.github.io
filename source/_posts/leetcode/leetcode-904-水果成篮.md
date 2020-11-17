---
title: leetcode-904. 水果成篮
date: 2020-09-23 11:56:00
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetc0de-904. 水果成篮](https://leetcode-cn.com/problems/fruit-into-baskets/)

**原始思路：使用滑动窗口**

```java
class Solution {
    public int totalFruit(int[] tree) {
        int n = tree.length;
        if (n == 0) {
            return 0;
        }
        // k记录种类
        List<Integer> list = new ArrayList<>();
        HashSet<Integer> set = new HashSet<>();
        int i = 0;
        int j = 0;
        int result = 0;
        while (i < n && j < n) {
            list.add(tree[j]);
            set.add(tree[j]);
            j++;
            // 达到收缩的条件
            while (i < n && set.size() > 2) {
                list.remove((Integer) tree[i]);
                i++;
                set = new HashSet<>(list);
            }
            result = Math.max(result, j - i);
        }
        return result;
    }
}
```

