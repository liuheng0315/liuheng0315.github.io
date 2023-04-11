---
title: leetcode-297. 二叉树的序列化与反序列化
date: 2023-04-12 00:10:47
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-297. 二叉树的序列化与反序列化](https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
import java.util.*;
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        if(root == null){
            return list.toString();
        }

        // 宽度优先遍历
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while(!queue.isEmpty()){
            // 队列不为空,取出队头数据
            TreeNode node = queue.poll();

            if(node == null){
                 list.add(null);
            }else{
               list.add(node.val); 
            }
    
            if(node != null){
                queue.offer(node.left);
                queue.offer(node.right);
            }
        }
        return list.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if(data == null || data.length() == 0){
            return null;
        }

        String str = data.substring(1, data.length() - 1);

        if(str.length() == 0) {
            return null;
        }

        String[] arr = str.split(",");
        List<Integer> list = new ArrayList<>();
        for(String s : arr){
            if(!"null".equals(s.trim())){
                list.add(Integer.valueOf(s.trim()));
            }else{
                list.add(null);
            }    
        }
        int cur = 0;
        Integer val = list.get(cur++);
        TreeNode root = new TreeNode(val);
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();

            if(node == null){
                continue;
            }

            if(cur == list.size() - 1){
                break;
            }

            Integer leftVal = list.get(cur++);
            TreeNode left = null;
            if(leftVal != null){
                left = new TreeNode(leftVal);
            }
            node.left = left;
            queue.offer(left);

            if(cur == list.size() - 1){
                break;
            }

            Integer rightVal = list.get(cur++);
            TreeNode right = null;
            if(rightVal != null){
                right = new TreeNode(rightVal);
            }
            node.right = right;
            queue.offer(right);
        }

        return root;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec ser = new Codec();
// Codec deser = new Codec();
// TreeNode ans = deser.deserialize(ser.serialize(root));
```

