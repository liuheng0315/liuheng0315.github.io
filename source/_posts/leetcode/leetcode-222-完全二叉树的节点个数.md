---
title: leetcode-222. 完全二叉树的节点个数
date: 2020-11-24 09:06:16
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [leetcode-222. 完全二叉树的节点个数](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

```java
class Solution {
    public int countNodes(TreeNode root) {
        int count = 0;
        if(root == null){
            return count;
        }
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()){
            int n = queue.size();
            for(int i = 0; i < n; i++){
                TreeNode node = queue.poll();
                count++;
                if(node.left != null){
                    queue.offer(node.left);
                }
                if(node.right != null){
                    queue.offer(node.right);
                }
            }
        }
        return count;
    }
}
```

