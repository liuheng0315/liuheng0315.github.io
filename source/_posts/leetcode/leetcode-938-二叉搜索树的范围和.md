---
title: leetcode-938. 二叉搜索树的范围和
date: 2021-04-27 09:14:49
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [938. 二叉搜索树的范围和](https://leetcode-cn.com/problems/range-sum-of-bst/)

```java
class Solution {
    int ans = 0;
    public int rangeSumBST(TreeNode root, int low, int high) {
        dfs(root, low, high);
        return ans;
    }

    public void dfs(TreeNode root, int low, int high){
        if(root == null){
            return;
        }
        dfs(root.left, low, high);
        if(root.val >=low && root.val <= high){
            ans += root.val;
        }
        dfs(root.right, low, high);
    }
}
```

