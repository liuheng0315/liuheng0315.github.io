---
title: leetcode-1123. 最深叶节点的最近公共祖先
date: 2020-11-12 13:43:30
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [1123. 最深叶节点的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-deepest-leaves/)

**原始思路：**

```java
class Solution {
    LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
    HashSet<TreeNode> deepSet = new HashSet<>();
    TreeNode ans;
    boolean bool = true;
    public TreeNode lcaDeepestLeaves(TreeNode root) {
        search(root);
        ans = root;
        dfs(root, new HashSet<TreeNode>());
        return ans;
    }

    public HashSet<TreeNode> dfs(TreeNode root, HashSet<TreeNode> allSet){
        if (root == null || !bool) {
            return allSet;
        }

        HashSet<TreeNode> leftSet = new HashSet<TreeNode>();
        if (root.left != null){
            leftSet = dfs(root.left, leftSet);
        }

        HashSet<TreeNode> rightSet = new HashSet<TreeNode>();
        if (root.right != null) {
            rightSet = dfs(root.right, rightSet);
        }
        leftSet.addAll(rightSet);
        allSet.addAll(leftSet);
        allSet.add(root);
        if (allSet.containsAll(deepSet) && bool) {
            ans = root;
            bool = false;
        }
        return allSet;
    }

    public void search(TreeNode root){
        queue.offer(root);
        while (!queue.isEmpty()){
            int n = queue.size();
            deepSet.clear();
            for (int i = 0; i < n; i++) {
                TreeNode node = queue.poll();
                deepSet.add(node);

                if (node.left != null){
                    queue.offer(node.left);
                }
                if (node.right != null){
                    queue.offer(node.right);
                }
            }
        }
    }
}

```



**左右递归寻找最大深度**

```java
class Solution {
    public TreeNode lcaDeepestLeaves(TreeNode root) {
        if (root == null) {
            return root;
        }
        int left = dfs(root.left);
        int right = dfs(root.right);
        if (left == right){
            return root;
        } else if (left > right) {
            return lcaDeepestLeaves(root.left);
        }else {
            return lcaDeepestLeaves(root.right);
        }
    }

    public int dfs(TreeNode root){
        if (root == null) {
            return 0;
        }
        int left = dfs(root.left);
        int right = dfs(root.right);
        return Math.max(left, right) + 1;
    }
}
```