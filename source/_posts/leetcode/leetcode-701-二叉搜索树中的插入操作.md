---
title: leetcode-701. 二叉搜索树中的插入操作
date: 2020-09-30 11:29:08
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-701. 二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

**原始思路**

```java
class Solution {

    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
           return new TreeNode(val);
        }
        dfs(root, val);
        return root;
    }

    public void dfs(TreeNode root, int val) {
        if (root == null) {
            return;
        }

        if (root.val > val){
            if (root.left == null) {
                TreeNode node = new TreeNode(val);
                root.left = node;
                return;
            }
            dfs(root.left, val);
        }

        if (root.val < val){
            if (root.right == null) {
                TreeNode node = new TreeNode(val);
                root.right = node;
                return;
            }
            dfs(root.right, val);
        }

    }
}
```

