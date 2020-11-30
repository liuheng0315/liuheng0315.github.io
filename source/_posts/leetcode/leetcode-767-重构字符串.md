---
title: leetcode-767. 重构字符串
date: 2020-11-30 21:02:17
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-767. 重构字符串](https://leetcode-cn.com/problems/reorganize-string/)

```java
class Solution {
    public String reorganizeString(String S) {
        String ans = "";
        char[] ch = S.toCharArray();
        Map<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < ch.length; i++) {
            map.put(ch[i], map.getOrDefault(ch[i], 0) + 1);
        }

        PriorityQueue<Character> queue = new PriorityQueue<>((o1, o2) -> map.get(o2).compareTo(map.get(o1)));

        for(Map.Entry<Character, Integer> entry : map.entrySet()){
            // 入队
            queue.offer(entry.getKey());
        }
        Character pre = null;
        while (!queue.isEmpty()){
            Character c1 = queue.poll();
            Character c2 = queue.poll();
            if (c1 == pre && c2 == null) {
                return "";
            }

            if (c1 != pre){
                Integer c1Count = map.get(c1);
                map.put(c1, c1Count - 1);
                if (c1Count > 1) {
                    // 再次入队
                    queue.offer(c1);
                }
                if(c2 != null){
                    queue.offer(c2);
                }
                pre = c1;
                ans += c1;
            }else {
                // 取c2
                Integer c2Count = map.get(c2);
                map.put(c2, c2Count - 1);
                if (c2Count > 1) {
                    // 再次入队
                    queue.offer(c2);
                }
                queue.offer(c1);
                pre = c2;
                ans += c2;
            }
        }
        return ans;
    }
}
```

