---
title: leetcode-24. 两两交换链表中的节点
date: 2020-10-13 09:43:35
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

**原始思路**

```java
class Solution {
    ListNode root = null;
    public ListNode swapPairs(ListNode head) {
        if(head == null){
            return head;
        }

        return dfs(head);
    }

    public ListNode dfs(ListNode root){
        if(root == null){
            return root;
        }

        ListNode node1 = new ListNode(root.val);

        if(root.next == null){
            return node1;
        }

        ListNode node2 = new ListNode(root.next.val);
        node2.next = node1;
        
        node1.next = dfs(root.next.next);

        return node2;
    }
}
```

