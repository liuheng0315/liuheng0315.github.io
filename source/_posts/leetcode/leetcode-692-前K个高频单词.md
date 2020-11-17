---
title: leetcode-692. 前K个高频单词
date: 2020-10-20 13:53:33
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-692. 前K个高频单词](https://leetcode-cn.com/problems/top-k-frequent-words/)

```java
class Solution {
    int n = 100010;
    int[][] st = new int[n][26];
    int[] count = new int[n];
    int idx = 0;
    Map<String, Integer> map = new HashMap<>();
    public List<String> topKFrequent(String[] words, int k) {
        for (String str : words) {
            insert(str);
        }

        PriorityQueue<String> queue = new PriorityQueue<>(k, (o1,o2)->{
            if (map.get(o2) == map.get(o1)){
                return o1.compareTo(o2);
            }
            return map.get(o2) - map.get(o1);
        });

        for (String str : words) {
            int cnt = search(str);
            map.put(str, cnt);
            if (!queue.contains(str)) {
                queue.offer(str);
            }
        }

        List<String> res = new ArrayList<>();
        while (k > 0){
            res.add(queue.poll());
            k--;
        }
        return res;
    }

    public void insert(String str){
        int p = 0;
        for (int i = 0; i < str.length(); i++) {
            int j = str.charAt(i) - 'a';
            if (st[p][j] == 0) {
                st[p][j] = ++idx;
            }
            p = st[p][j];
        }
        count[p]++;
    }

    public int search(String str){
        int p = 0;
        for (int i = 0; i < str.length(); i++) {
            int j = str.charAt(i) - 'a';
            if (st[p][j] == 0) {
                return 0;
            }
            p = st[p][j];
        }
        return count[p];
    }
}
```

