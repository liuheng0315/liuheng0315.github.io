---
title: leetcode-61. 旋转链表
date: 2020-10-19 22:48:10
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-61. 旋转链表](https://leetcode-cn.com/problems/rotate-list/)

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        ListNode tmp = head;
        int count = 0;
        ListNode end = null;
        while(tmp != null){
            if(tmp.next == null){
                end = tmp;
            }
            tmp = tmp.next;
            count++;
        }

        if(count == 0){
            return head;
        }

        int i = k % count;
        int j = count - i - 1;
        tmp = head;
        while(j > 0){
            tmp = tmp.next;
            j--;
        }

        if(tmp.next != null){
            ListNode result = tmp.next;
            tmp.next = null;
            end.next = head;
            return result;
        }else{
            return head;
        }
    }
}
```

