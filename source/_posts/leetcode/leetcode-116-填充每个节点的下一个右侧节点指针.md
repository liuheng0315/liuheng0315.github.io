---
title: leetcode-116. 填充每个节点的下一个右侧节点指针
date: 2020-10-15 10:14:07
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```java
class Solution {
    LinkedList<Node> queue = new LinkedList<>();
    public Node connect(Node root) {
        if(root == null){
            return root;
        }
        queue.offer(root);
        while(!queue.isEmpty()){
            int n = queue.size();
            Node pre = null;
            for(int i = 0; i < n; i++){
                Node node = queue.poll();
                if(pre != null){
                    pre.next = node;
                    pre = node;
                }else{
                    pre = node;
                }
                
                if(node.left != null){
                    queue.offer(node.left);
                }
                if(node.right != null){
                    queue.offer(node.right);
                }
            }
            pre.next = null;
        }
        return root;
    }
}
```

