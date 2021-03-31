---
title: leetcode-83. 删除排序链表中的重复元素
date: 2021-03-26 13:40:58
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode pre = head;
        while(pre!=null && pre.next != null){
            if(pre.next.val == pre.val){
                pre.next = pre.next.next;
            }else{
                pre = pre.next;
            }
        }
        return head;
    }
}
```

