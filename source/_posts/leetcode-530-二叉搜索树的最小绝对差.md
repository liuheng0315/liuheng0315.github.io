---
title: leetcode-530. 二叉搜索树的最小绝对差
date: 2020-10-12 09:22:46
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-530. 二叉搜索树的最小绝对差](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/)

**原始思路**

```java
class Solution {
    List<Integer> list = new ArrayList<Integer>();
    int result = Integer.MAX_VALUE;
    public int getMinimumDifference(TreeNode root) {
        dfs(root);
        for(int i = 1; i < list.size(); i++){
            result = Math.min(result,list.get(i)-list.get(i-1));
        }
        return result;
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

