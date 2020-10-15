---
title: leetcode-1161. 最大层内元素和
date: 2020-09-28 18:43:09
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1161. 最大层内元素和](https://leetcode-cn.com/problems/maximum-level-sum-of-a-binary-tree/)

```java
class Solution {
    public int maxLevelSum(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int max = Integer.MIN_VALUE;
        int result = 0;
        int level = 0;
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (queue.size() > 0) {
            int n = queue.size();
            int count = 0;
            level++;
            for (int i = 0; i < n; i++) {
                TreeNode node = queue.poll();
                count += node.val;
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            if (max < count){
                max = count;
                result = level;
            }
        }
        return result;
    }
}
```

