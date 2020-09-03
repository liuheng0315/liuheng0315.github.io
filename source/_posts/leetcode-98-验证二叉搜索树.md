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



**BFS思路**

