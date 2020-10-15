---
title: leetcode-1347. 制造字母异位词的最小步骤数
date: 2020-10-12 13:06:52
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1347. 制造字母异位词的最小步骤数](https://leetcode-cn.com/problems/minimum-number-of-steps-to-make-two-strings-anagram/)

**原始思路**

```java
class Solution {
    public int minSteps(String s, String t) {
        Map<Character, Integer> sMap = new HashMap<>();
        Map<Character, Integer> tMap = new HashMap<>();

        for (int i = 0; i < s.length(); i++) {
            sMap.put(s.charAt(i), sMap.getOrDefault(s.charAt(i), 0) + 1);
            tMap.put(t.charAt(i), tMap.getOrDefault(t.charAt(i), 0) + 1);
        }

        int ans = 0;
        for(Map.Entry<Character, Integer> entry : tMap.entrySet()){
            char c = entry.getKey();
            int value = entry.getValue();
            if (sMap.containsKey(c)) {
                if (sMap.get(c) < value) {
                    ans += value - sMap.get(c);
                }
            }else {
                ans += value;
            }
        }

        return ans;
    }
}
```

