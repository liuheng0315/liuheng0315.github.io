---
title: leetcode-236.二叉树的最近公共祖先
date: 2020-09-07 11:56:44
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

**原始思路**

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        ArrayList<TreeNode> listP = new ArrayList<>();
        ArrayList<TreeNode> listQ = new ArrayList<>();
        findNode(root, p, listP);
        findNode(root, q, listQ);

        //数组倒序遍历,最近的公共数字就是最近公共祖先
        for(int i = listP.size() -1; i >=0; i--){
            if(listQ.contains(listP.get(i))){
                return listP.get(i);
            }
        }
        return null;
    }

    //回溯算法
    public boolean findNode(TreeNode root, TreeNode target, ArrayList<TreeNode> list){

        //遍历到最后还没找到返回false
        if(root == null){
            return false;
        }

        //如果没有找到目标节点,添加节点到路径中
        if (!list.contains(target)){
            list.add(root);
        }

        //如果找到了目标节点,则返回
        if(list.contains(target)){
            return true;
        }

        //DFS查找
        if(!findNode(root.left, target, list)){
            list.remove(root.left);
        }else {
            return true;
        }

        if(!findNode(root.right, target, list)){
            list.remove(root.right);
        }else {
            return true;
        }

        return false;
    }
}
```



**题解思路**

```java
class Solution{
     public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
         if(root == null || root == p || root == q){
             return root;
         }
         TreeNode left = lowestCommonAncestor(root.left, p, q);
         TreeNode right = lowestCommonAncestor(root.right, p, q);
         // 最近公共祖先在右边
         if(left == null){
             return right;
         }
         // 最近公共祖先在左边边
         if(right == null){
             return left;
         }
         //p q分散在两边
         return root;
     }
}
```

