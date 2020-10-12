---
title: leetcode-1339. 分裂二叉树的最大乘积
date: 2020-10-12 13:58:45
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1339. 分裂二叉树的最大乘积](https://leetcode-cn.com/problems/maximum-product-of-splitted-binary-tree/)

**原始思路**

```java
class Solution {
    long max = 0;
    TreeNode r;
    Map<TreeNode, Long> map = new HashMap<>();
    public int maxProduct(TreeNode root) {
        // 初始化map
        dfs(root);
        r = root;

        //求最大乘积
        dfs2(root);
        return (int)(max % 1000000007);
    }

    public void dfs2(TreeNode root) {
        if (root == null) {
            return;
        }

        // 切割左子树
        if (root.left != null) {
            max =Math.max(max, map.get(root.left) * (map.get(r) - map.get(root.left)));
            dfs2(root.left);
        }

        // 切割右子树
        if (root.right != null) {
            max =Math.max(max, map.get(root.right) * (map.get(r) - map.get(root.right)));
            dfs2(root.right);
        }
    }

    public void dfs(TreeNode root) {
        if (root == null) {
            return;
        }
        if (root.left != null) {
            dfs(root.left);
        }
        if (root.right != null) {
            dfs(root.right);
        }

        map.put(root, map.getOrDefault(root, 0L) + root.val);

        if (root.left != null) {
            map.put(root, map.getOrDefault(root.left, 0L) + map.get(root));
        }

        if (root.right != null) {
            map.put(root, map.getOrDefault(root.right, 0L) + map.get(root));
        }
    }
}
```

