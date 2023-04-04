---
title: leetcode-543. 二叉树的直径
date: 2021-06-04 14:04:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

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
    int max = 0;
    public int diameterOfBinaryTree(TreeNode root) {
        //左边能达到的最大长度+右边能达到的最大长度
        if(root == null){
            return 0;
        }
        dfs(root);
        return max;
    }

    public int dfs(TreeNode root){
        if(root == null){
            return 0;
        }

        int left = 0;
        if(root.left != null){
            left = Math.max(left, 1 + dfs(root.left));
        }

        int right = 0;
        if(root.right != null){
            right = Math.max(right, 1 + dfs(root.right));
        }
        
        max = Math.max(max, left + right);
        return Math.max(left, right);
    }
}
```

