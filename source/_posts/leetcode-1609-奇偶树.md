---
title: leetcode-1609. 奇偶树
date: 2020-10-10 11:56:45
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1609. 奇偶树](https://leetcode-cn.com/problems/even-odd-tree/)

**原始思路**

```java
class Solution {
    public boolean isEvenOddTree(TreeNode root) {
        LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
        queue.offer(root);
        boolean b = true;
        int level = 0;
        while (!queue.isEmpty()){
            int n = queue.size();
            int tmp = Integer.MAX_VALUE;
            int tmp2 = Integer.MIN_VALUE;
            for (int i = 0; i < n; i++) {
                TreeNode node = queue.poll();
                if (level % 2 == 0){
                    //偶数层
                    if (node.val % 2 == 0){
                        b = false;
                    }

                    if (node.val <= tmp2){
                        b = false;
                    }
                    tmp2 = node.val;
                }else {
                    //奇数层
                    if (node.val % 2 != 0){
                        b = false;
                    }

                    if (node.val >= tmp){
                        b = false;
                    }
                    tmp = node.val;
                }

                if (!b){
                    return b;
                }

                if(node.left != null){
                    queue.offer(node.left);
                }
                if (node.right != null){
                    queue.offer(node.right);
                }
            }
            level++;
        }
        return b;
    }
}
```

