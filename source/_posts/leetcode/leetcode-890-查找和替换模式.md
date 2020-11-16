---
title: leetcode-890. 查找和替换模式
date: 2020-09-30 13:25:25
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-890. 查找和替换模式](https://leetcode-cn.com/problems/find-and-replace-pattern/)

**原始思路**

```java
class Solution {
    public List<String> findAndReplacePattern(String[] words, String pattern) {
        List<String> result = new ArrayList<>();
        Map<Character, Character> map = new HashMap<>();
        Map<Character, Character> rmap = new HashMap<>();
        for (int i = 0; i < words.length; i++) {
            map.clear();
            rmap.clear();
            if (words[i].length() != pattern.length()) {
                continue;
            }

            boolean b = true;
            for (int j = 0; j < words[i].length(); j++) {
                // 进行模式比较
                char chr1 = words[i].charAt(j);
                char chr2 = pattern.charAt(j);
                if (!map.containsKey(chr2) && !rmap.containsKey(chr1) ) {
                    map.put(chr2, chr1);
                    rmap.put(chr1, chr2);
                }else {
                    if (map.containsKey(chr2) && map.get(chr2) != chr1) {
                        b = false;
                        break;
                    }

                    if (rmap.containsKey(chr1) && rmap.get(chr1) != chr2) {
                        b = false;
                        break;
                    }
                }
            }

            if (b) {
                result.add(words[i]);
            }
        }

        return result;
    }
}
```

