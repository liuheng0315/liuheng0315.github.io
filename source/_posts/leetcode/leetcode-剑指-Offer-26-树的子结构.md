---
title: leetcode-剑指 Offer 26. 树的子结构
date: 2020-10-15 19:11:58
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-剑指 Offer 26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if (A != null && B == null){
            return false;
        }
        return dfs(A, B);
    }

    public boolean dfs(TreeNode root, TreeNode B){
        if (root == null){
            return false;
        }

        if (root.val == B.val){
            // 开始比较
            if (compare(root, B)){
                return true;
            }
        }

        if (root.left != null){
            if (dfs(root.left, B)){
                return true;
            }
        }

        if (root.right != null){
            if (dfs(root.right, B)){
                return true;
            }
        }

        return false;
    }

    public boolean compare(TreeNode A, TreeNode B){
        if (A == null && B == null){
            return true;
        }
        if (A != null && B == null) {
            return true;
        }
        if (A == null && B != null) {
            return false;
        }

        if (A.val != B.val){
            return false;
        }

        return compare(A.left, B.left) && compare(A.right, B.right);
    }
}
```

