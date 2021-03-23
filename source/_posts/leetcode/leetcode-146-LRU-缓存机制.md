---
title: leetcode-146. LRU 缓存机制
date: 2021-03-12 12:51:00
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [146. LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)

```java
class LRUCache {
    int capacity = 0;
    LinkedList queue = new LinkedList<Integer>();
    Map<Integer, Integer> map = new HashMap<>();
    public LRUCache(int capacity) {
        this.capacity = capacity;
    }
    
    public int get(int key) {
        if(!map.containsKey(key)){
            return -1;
        }
        queue.remove((Integer) key);
        queue.offerFirst(key);
        return map.get(key);
    }
    
    public void put(int key, int value) {
        if(map.containsKey(key)){
            queue.remove((Integer) key);
            queue.offerFirst(key);
            map.put(key, value);
            return;
        }

        if(queue.size() == capacity){
            map.remove(queue.peekLast());
            queue.removeLast();
        }
        queue.offerFirst(key);
        map.put(key, value);
    }
}
```

