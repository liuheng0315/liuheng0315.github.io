---
title: leetcode-142. 环形链表 II
date: 2020-10-10 09:30:10
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

**原始思路**

```java
public class Solution {
    Set<ListNode> set = new HashSet<>();
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }
        if (set.contains(head)){
            return head;
        }
        set.add(head);
        if (head.next!=null){
            return detectCycle(head.next);
        }
        return null;
    }
}
```

