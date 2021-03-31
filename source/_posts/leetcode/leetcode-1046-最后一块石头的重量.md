---
title: leetcode-1046. 最后一块石头的重量
date: 2021-03-30 10:35:44
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1046. 最后一块石头的重量](https://leetcode-cn.com/problems/last-stone-weight/)

```java
class Solution {
    public int lastStoneWeight(int[] stones) {
        Queue<Integer> queue = new PriorityQueue<>((o1,o2)->{
            return o2.compareTo(o1);
        });

        for(int w :stones){
            queue.offer(w);
        }
        while (queue.size() > 1){
            int w1 = queue.poll();
            int w2 = queue.poll();
            if (w1 == w2){
                continue;
            }
            int r = Math.abs(w1 - w2);
            queue.offer(r);
        }

        return queue.isEmpty() ? 0 : queue.poll();
    }
}
```

