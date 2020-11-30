---
title: leetcode-1456. 定长子串中元音的最大数目
date: 2020-11-30 20:21:22
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..	
---

#### [leetcode-1456. 定长子串中元音的最大数目](https://leetcode-cn.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/)

```java
class Solution {
    public int maxVowels(String s, int k) {
        Set<Character> set = new HashSet<>();
        set.add('a');
        set.add('e');
        set.add('i');
        set.add('o');
        set.add('u');

        int ans = 0;
        int x = 0;
        int i = 0;
        int j = 0;
        int n = s.length();
        while(j < n){
            if(set.contains(s.charAt(j))){
                x ++;
            }
            j++;
            while(j - i == k){
                ans = Math.max(ans, x);
                if(set.contains(s.charAt(i))){
                    x --;
                }
                i ++;
            }
        }
        return ans;
    }
}
```

