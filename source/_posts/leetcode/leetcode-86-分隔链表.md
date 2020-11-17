---
title: leetcode-86. 分隔链表
date: 2020-10-15 19:49:59
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-86. 分隔链表](https://leetcode-cn.com/problems/partition-list/)

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode tmp = head;
        ListNode rootMin = null;
        ListNode rootMax = null;
        ListNode min = null;
        ListNode max = null;
        while (tmp != null) {
            if (tmp.val < x){
                if (min == null) {
                    min = new ListNode(tmp.val);
                    rootMin = min;
                }else {
                    min.next = new ListNode(tmp.val);
                    min = min.next;
                }
            }else {
                if (max == null){
                    max = new ListNode(tmp.val);
                    rootMax = max;
                }else {
                    max.next = new ListNode(tmp.val);
                    max = max.next;
                }
            }
            tmp = tmp.next;
        }

        if (min == null) {
            return rootMax;
        }else {
            min.next = rootMax;
            return rootMin;
        }
    }
}
```

