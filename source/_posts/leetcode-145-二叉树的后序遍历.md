---
title: leetcode-145. 二叉树的后序遍历
date: 2020-09-29 09:10:06
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-145. 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

```java
class Solution {
    List<Integer> result = new ArrayList<>();
    public List<Integer> postorderTraversal(TreeNode root) {
        dfs(root);
        return result;
    }

    public void dfs(TreeNode root){
        if(root == null){
            return;
        }

        if(root.left != null){
            dfs(root.left);
        }

        if(root.right != null){
            dfs(root.right);
        }

        if(root != null){
            result.add(root.val);
        }
    }
}
```