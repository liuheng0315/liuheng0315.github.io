---
title: leetcode-面试题 17.11. 单词距离
date: 2020-10-13 11:32:48
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-面试题 17.11. 单词距离](https://leetcode-cn.com/problems/find-closest-lcci/)

**原始思路**

```java
class Solution {
    Map<String, List<Integer>> map = new HashMap<>();
    public int findClosest(String[] words, String word1, String word2) {
        for (int i = 0; i < words.length; i++) {
            if (map.containsKey(words[i])) {
                List<Integer> list = map.get(words[i]);
                list.add(i);
                map.put(words[i], list);
            }else {
                List<Integer> tmp = new ArrayList<>();
                tmp.add(i);
                map.put(words[i], tmp);
            }
        }

        List<Integer> list1 = map.get(word1);
        List<Integer> list2 = map.get(word2);

        int result = Integer.MAX_VALUE;
        for (int i = 0; i < list1.size(); i++) {
            int frist = list1.get(i);
            for (int j = 0; j < list2.size(); j++) {
                int sec = list2.get(j);
                int ans = Math.abs(frist - sec);
                result = Math.min(result, ans);
            }
        }
        return result;
    }
}
```



**题解思路**

```java
class Solution {
    Map<String, List<Integer>> map = new HashMap<>();
    public int findClosest(String[] words, String word1, String word2) {
        int i = -1;
        int j = -1;
        int result = Integer.MAX_VALUE;
        for (int k = 0; k < words.length; k++) {
            if (words[k].equals(word1)) {
                i = k;
            }
            if (words[k].equals(word2)) {
                j = k;
            }

            if (i != -1 && j != -1) {
                result = Math.min(result, Math.abs(i - j));
            }
        }
        return result;
    }
}
```

