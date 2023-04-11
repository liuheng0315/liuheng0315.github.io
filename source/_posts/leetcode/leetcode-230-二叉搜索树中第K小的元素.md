---
title: leetcode-230. 二叉搜索树中第K小的元素
date: 2023-04-12 00:05:15
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-230. 二叉搜索树中第K小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/description/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        Queue<Integer> queue = new PriorityQueue<>(k, (o1, o2) -> {
            return o1 - o2;
        });

        dfs(root, queue);

        int res = -1;
        while(true){
            int v = queue.poll();
            k--;

            if(k == 0){
                res = v;
            }
            if( k <= 0){
                break;
            }
        }

        return res;
    }

    public void dfs(TreeNode root,  Queue<Integer> queue){
        if(root == null){
            return;
        }
        queue.offer(root.val);

        dfs(root.left, queue);
        dfs(root.right, queue);
    }
}
```

