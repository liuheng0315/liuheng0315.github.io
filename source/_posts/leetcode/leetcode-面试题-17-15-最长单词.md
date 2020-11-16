---
title: leetcode-面试题 17.15. 最长单词
date: 2020-10-10 15:23:37
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-面试题 17.15. 最长单词](https://leetcode-cn.com/problems/longest-word-lcci/)

**题解思路**

```java
class Solution {

    public String longestWord(String[] words) {
        Arrays.sort(words,(o1,o2)->{
            if(o1.length() == o2.length())
                return o1.compareTo(o2);
            else{
                return Integer.compare(o2.length(),o1.length());
            }
        });

        Set<String> set = new HashSet<>(Arrays.asList(words));
        for (String str : words) {
            set.remove(str);
            if (dfs(set, str)){
                return str;
            }
        }
        return "";
    }

    public boolean dfs(Set<String> set, String target){
        if (target.length() == 0) {
            return true;
        }
        for (int i = 0; i < target.length(); i++) {
            //截取0-i的长度的子字符串
            if (set.contains(target.substring(0, i + 1)) && dfs(set, target.substring(i + 1))) {
                return true;
            }
        }
        return false;
    }
}
```

