---
title: leetcode-129. 求根到叶子节点数字之和
date: 2020-10-29 09:27:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-129. 求根到叶子节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)

```java
class Solution {
    int ans = 0;
    public int sumNumbers(TreeNode root) {
        if(root == null){
            return ans;
        }
        dfs(root, root.val);
        return ans;
    }

    public void dfs(TreeNode root, int val){
        if(root.left == null && root.right == null){
            ans += val;
            return;
        }
        if(root.left != null){
            dfs(root.left, val*10 + root.left.val);
        }
        if(root.right != null){
            dfs(root.right, val*10 + root.right.val);
        }
    }
}
```

