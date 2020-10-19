---
title: leetcode-剑指 Offer 24. 反转链表
date: 2020-10-19 23:47:59
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-剑指 Offer 24. 反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

**双指针**

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head.next == null){
            return head;
        }

        ListNode cur = head;
        ListNode pre = null;
        while(cur != null){
            cur.next = pre;
            pre = cur;
        }
    }
}
```



**递归**

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null || head.next == null){
            return head;
        }
        ListNode res = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return res;
    }
}
```

