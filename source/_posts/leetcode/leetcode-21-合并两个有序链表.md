---
title: leetcode-21. 合并两个有序链表
date: 2021-08-03 20:30:46
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == null){
            return l2;
        }

        if(l2 == null){
            return l1;
        }

        // 以l1为主
        ListNode cur1 = l1;
        ListNode cur2 = l2;
        ListNode root = new ListNode(-1);
        ListNode cur = root;

        while(cur1 != null && cur2 != null){
            if(cur1.val <= cur2.val){
                cur.next = cur1;
                cur = cur.next;
                cur1 = cur1.next;
            }else{
                cur.next = cur2;
                cur = cur.next;
                cur2 = cur2.next;
            }
        }

        // 遍历链表还没走完的部分
        if(cur1 != null){
            cur.next = cur1;
        }

        if(cur2 != null){
            cur.next = cur2;
        }

        return root.next;
    }
}
```

