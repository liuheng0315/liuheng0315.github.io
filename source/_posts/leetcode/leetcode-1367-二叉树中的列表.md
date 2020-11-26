---
title: leetcode-1367. 二叉树中的列表
date: 2020-11-26 20:51:39
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-1367. 二叉树中的列表](https://leetcode-cn.com/problems/linked-list-in-binary-tree/)

```java
class Solution {
    public boolean isSubPath(ListNode head, TreeNode root) {
        if(root == null){
            return false;
        }
        return dfs(head, root) || isSubPath(head, root.left) || isSubPath(head, root.right);
    }

    public boolean dfs(ListNode head, TreeNode root){
        if(head == null){
            return true;
        }

        if(root == null){
            return false;
        }

        if(head.val != root.val){
            return false;
        }
        return dfs(head.next, root.left) || dfs(head.next, root.right);
    }
}
```

