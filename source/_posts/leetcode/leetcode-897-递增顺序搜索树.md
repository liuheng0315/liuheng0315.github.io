---
title: leetcode-897. 递增顺序搜索树
date: 2021-04-25 09:11:41
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [897. 递增顺序搜索树](https://leetcode-cn.com/problems/increasing-order-search-tree/)

```java
class Solution {
    TreeNode r = new TreeNode();
    public TreeNode increasingBST(TreeNode root) {
        TreeNode res = r;
        dfs(root);
        return res.right;
    }

    public void dfs(TreeNode root){
        if(root == null){
            return;
        }
        dfs(root.left);
        TreeNode tmp = new TreeNode(root.val);
        r.right = tmp;
        r = tmp;

        dfs(root.right);
    }
}
```

