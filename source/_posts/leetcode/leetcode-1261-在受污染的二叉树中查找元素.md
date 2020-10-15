---
title: leetcode-1261. 在受污染的二叉树中查找元素
date: 2020-10-13 14:40:00
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1261. 在受污染的二叉树中查找元素](https://leetcode-cn.com/problems/find-elements-in-a-contaminated-binary-tree/)

**原始思路**

```java
class FindElements {
    Set<Integer> set = new HashSet<>();
    public FindElements(TreeNode root) {
        dfs(root, null, 0);
    }

    public void dfs(TreeNode root, Integer rootValue, int flag) {
        if (root == null) {
            return;
        }

        if (rootValue == null) {
            root.val = 0;
        }else {
            if (flag == 0) {
                root.val = 2*rootValue + 1;
            }else {
                root.val = 2*rootValue + 2;
            }
        }
        set.add(root.val);

        if (root.left != null) {
            dfs(root.left, root.val, 0);
        }

        if (root.right != null){
            dfs(root.right, root.val, 1);
        }
    }
    
    public boolean find(int target) {
        if (set.contains(target)) {
            return true;
        }
        return false;
    }
}
```

