---
title: leetcode-783. 二叉搜索树节点最小距离
date: 2021-04-13 09:13:39
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [783. 二叉搜索树节点最小距离](https://leetcode-cn.com/problems/minimum-distance-between-bst-nodes/)

```java
class Solution {
    int ans = Integer.MAX_VALUE;
    List<Integer> list = new ArrayList<>();
    public int minDiffInBST(TreeNode root) {
        dfs(root);
        for(int i = 1; i < list.size(); i++){
            ans = Math.min(ans, list.get(i) - list.get(i-1));
        }
        return ans == Integer.MAX_VALUE ? 0 : ans;
    }

    public void dfs(TreeNode root){
        if(root == null){
            return;
        }

        if(root.left != null){
            dfs(root.left);
        }
        
        list.add(root.val);

        if(root.right != null){
            dfs(root.right);
        }
    }
}
```

