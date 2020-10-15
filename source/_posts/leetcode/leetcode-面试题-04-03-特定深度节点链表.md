---
title: leetcode-面试题 04.03. 特定深度节点链表
date: 2020-10-12 15:05:10
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-面试题 04.03. 特定深度节点链表](https://leetcode-cn.com/problems/list-of-depth-lcci/)

**原始思路**

```java
class Solution {
    LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
    List<ListNode> list = new ArrayList<>();
    public ListNode[] listOfDepth(TreeNode tree) {
        if (tree == null) {
            return null;
        }

        queue.offer(tree);
        while (!queue.isEmpty()) {
            ListNode root = null;
            ListNode pre = null;
            int n = queue.size();
            for (int i = 0; i < n; i++) {
                TreeNode node = queue.poll();
                ListNode listNode = new ListNode();
                listNode.val = node.val;
                if (root == null) {
                    root = listNode;
                    pre = root;
                }else {
                    pre.next = listNode;
                    pre = listNode;
                }

                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null){
                    queue.offer(node.right);
                }
            }

            list.add(root);
        }

        ListNode[] ans = new ListNode[list.size()];
        for (int i = 0; i < list.size(); i++) {
            ans[i] = list.get(i);
        }
        return ans;
    }
}
```

