---
title: leetcode-763. 划分字母区间
date: 2020-10-22 12:47:07
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-763. 划分字母区间](https://leetcode-cn.com/problems/partition-labels/)

```java
class Solution {
    public List<Integer> partitionLabels(String S) {
        List<Integer> result = new ArrayList<>();
        int n = S.length();
        int pre = 0;
        Set<Character> set = new HashSet<>();
        for (int i = 0; i < n; i++) {
            set.add(S.charAt(i));
            boolean b = judge(set, S.substring(i + 1));
            if (!b) {
                result.add(i - pre + 1);
                pre = i + 1;
                set.clear();
            }
        }
        return result;
    }

    // 判断后面的字符串是否包含set中的内容
    public boolean judge(Set<Character> set, String s) {
        for (Character ch : set) {
            if (s.contains(ch.toString())) {
                return true;
            }
        }
        return false;
    }
}
```

