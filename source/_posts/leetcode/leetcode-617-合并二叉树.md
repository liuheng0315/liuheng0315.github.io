---
title: leetcode-617. 合并二叉树
date: 2020-09-23 10:10:11
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-617. 合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

**原始思路**

```java
class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 != null && t2 != null) {
            TreeNode node = new TreeNode();
            node.val = t1.val + t2.val;
            node.right = mergeTrees(t1.right, t2.right);
            node.left = mergeTrees(t1.left, t2.left);
            return node;
        } else if (t1 != null) {
            return t1;
        } else if (t2 != null) {
            return t2;
        }
        return null;
    }
}
```

