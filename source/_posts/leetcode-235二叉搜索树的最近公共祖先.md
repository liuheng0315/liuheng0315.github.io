---
title: leetcode-235.二叉搜索树的最近公共祖先
date: 2020-09-04 22:35:35
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-235二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

**原始思路**

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        return findAncestor(root, p, q);
    }
    public static TreeNode findAncestor(TreeNode root, TreeNode p, TreeNode q){
        //如果p,q分散在两边,那中间的肯定是共公祖先,当p、q其中一个为root的话,那么必然自己就是祖先
        if(p.val > root.val && q.val< root.val
            || p.val < root.val && q.val> root.val
            || p.val == root.val
            || q.val == root.val){
                return root;
            }
        
        //此时说明p、q必定分布在一边,选取任意一个值判断在左边还是右边,然后进行递归
        if(root.val > p.val){
            return findAncestor(root.left, p , q);
        }else{
            return findAncestor(root.right, p , q);
        }
    }
}
```

