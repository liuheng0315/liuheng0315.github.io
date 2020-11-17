---
title: leetcode-143. 重排链表
date: 2020-10-20 09:22:27
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode143. 重排链表](https://leetcode-cn.com/problems/reorder-list/)

```java
class Solution {
    public void reorderList(ListNode head) {
        LinkedList<ListNode> queue = new LinkedList<ListNode>();
        ListNode tmp = head;
        while (tmp != null){
            queue.offer(tmp);
            tmp = tmp.next;
        }

        ListNode cur = null;
        while (!queue.isEmpty()){
            ListNode frist = queue.pollFirst();
            ListNode last = queue.pollLast();
            frist.next = last;
            if (cur != null) {
                cur.next = frist;
            }
            if (last != null) {
                cur = last;
                cur.next = null;
            }
        }
    }
}
```

