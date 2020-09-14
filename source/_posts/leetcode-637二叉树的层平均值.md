---
title: leetcode-637二叉树的层平均值
date: 2020-09-14 16:50:37
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-637. 二叉树的层平均值](https://leetcode-cn.com/problems/average-of-levels-in-binary-tree/)

```java
class Solution {
    List<Double> result = new ArrayList<>();

    public List<Double> averageOfLevels(TreeNode root) {
        LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
        queue.add(root);
        while (!queue.isEmpty()) {
            long sum = 0;
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                //每次弹出来一个,代表一层遍历结束,计算每层的平均值
                sum += node.val;
                if (i == size - 1) {
                    result.add((double)sum / size);
                }

                if (node.left != null) {
                    queue.offer(node.left);
                }

                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
        }
        return result;
    }
}
```

