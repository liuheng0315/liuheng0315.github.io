---
title: leetcode-234. 回文链表
date: 2020-10-23 09:21:29
tags:
---

#### [leetcode-234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

**使用双端队列**

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        LinkedList<ListNode> queue = new LinkedList<ListNode>();
        ListNode tmp = head;
        while (tmp != null) {
            queue.offer(tmp);
            tmp = tmp.next;
        }

        while (!queue.isEmpty()){
            ListNode frist = queue.pollFirst();
            ListNode last = queue.pollLast();
            if (last == null) {
                return true;
            }
            if (frist.val != last.val){
                return false;
            }
        }
        return true;
    }
}
```

