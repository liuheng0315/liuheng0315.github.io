---
title: leetcode-1448. 统计二叉树中好节点的数目
date: 2020-11-09 12:33:45
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1448. 统计二叉树中好节点的数目](https://leetcode-cn.com/problems/count-good-nodes-in-binary-tree/)

```java
class Solution {
    int count = 0;
    public int goodNodes(TreeNode root) {
        dfs(root, Integer.MIN_VALUE);
        return count;
    }

    public void dfs(TreeNode root, int preMax){
        if (root == null) {
            return;
        }

        if (root.val >= preMax) {
            count++;
        }

        dfs(root.right, Math.max(preMax, root.val));
        dfs(root.left, Math.max(preMax, root.val));
    }
}
```

