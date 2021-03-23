---
title: leetcode-792. 匹配子序列的单词数
date: 2021-03-03 14:43:17
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [792. 匹配子序列的单词数](https://leetcode-cn.com/problems/number-of-matching-subsequences/)

```java
class Solution {
    Set<Character> set = new HashSet<>();
    Set<String> set2 = new HashSet<>();
    public int numMatchingSubseq(String S, String[] words) {
        int ans = 0;

        for (int i = 0; i < S.length(); i++) {
            set.add(S.charAt(i));
        }

        for (String word: words) {
            if (set2.contains(word) || compare(S, word)) {
                set2.add(word);
                ans++;
            }
        }
        return ans;
    }

    public boolean compare(String S, String word){
        int k = 0;
        int i = 0;

        for (i = 0; i < word.length(); i++) {
            if (!set.contains(word.charAt(i))) {
                return false;
            }

            // 一直找到第一个相等的
            while (k < S.length() && S.charAt(k) != word.charAt(i)){
                k++;
            }

            // 此时判断
            if (k >= S.length() || S.charAt(k) != word.charAt(i)) {
                // 说明没有找到相等的
                return false;
            }

            // 找到了相等,继续找下一个
            k++;
        }

        return true;
    }
}
```

