---
title: leetcode-814. 二叉树剪枝
date: 2021-06-04 14:23:34
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [814. 二叉树剪枝](https://leetcode-cn.com/problems/binary-tree-pruning/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode pruneTree(TreeNode root) {
        return dfs(root);
    }

    public TreeNode dfs(TreeNode root){
        if(root == null){
            return null;
        }

        if(root.left != null){
            root.left = dfs(root.left);
        }

        if(root.right != null){
            root.right = dfs(root.right);
        }

        if(root.val == 0 && root.left == null && root.right == null){
            root = null;
        }
        return root;
    }
}
```

