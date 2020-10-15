---
title: leetcode-98.验证二叉搜索树
date: 2020-09-02 13:34:08
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-98.验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

**原始思路**：**中序排序**

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
		if(root == null){
            return true;
        }
        AtomicReference<Integer> atomicReference = new AtomicReference<>();
        return isValid(root, atomicReference);
    }
    
    // 中序排序遍历,后遍历的树需要大于前一个数
    public static boolean isValid(TreeNode current, AtomicReference<Integer> atomicReference){
        if(current.left != null){
            if(!isValid(current.left, atomicReference)){
                return false;
            } 
        }
        
        if(atomicReference.get() != null && current.val <= atomicReference.get()){
            return false;
        }
        atomicReference.set(current.val);
        
        if(current.right != null){
            if(!isValid(current.right, atomicReference)){
                return false;
            }
        }
        return true;
    }
}
```



**递归思路**

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValid(root, null, null);
    }
    
    public boolean isValid(TreeNode root,Integer min, Integer max){
        if(root == null){
            return true;
        }
        
        // 重点注意:左子树的最大值不能大于等根节点,右子树的最小值不能小于等于根节点
        if(min != null && root.val <= min){
            return false;
        }
        if(max != null && root.val >= max){
            return false;
        }
        
        //递归左右子树
        if(!isValid(root.left, min, root.val)){
            return false;
        }
        if(!isValid(root.right, root.val, max)){
            return false;
        }
        return true;
    } 
}
```

