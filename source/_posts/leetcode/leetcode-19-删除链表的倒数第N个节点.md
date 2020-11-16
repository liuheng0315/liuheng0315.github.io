---
title: leetcode-19. 删除链表的倒数第N个节点
date: 2020-10-18 23:02:35
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-19. 删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        int count = 0;
        ListNode cur = head;
        while(cur.next != null){
            cur = cur.next;
            count ++;
        }

        cur = head;
        ListNode pre = null;

        int k = count - n + 1;
        while(k > 0){
            pre = cur;
            cur = cur.next;
            k--;
        }

        if(pre != null){
            pre.next = cur.next;
        }else{
            return cur.next;
        }

        return head;
    }
}
```

