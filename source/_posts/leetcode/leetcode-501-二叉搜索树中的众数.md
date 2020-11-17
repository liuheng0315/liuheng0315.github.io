---
title: leetcode-501. 二叉搜索树中的众数
date: 2020-09-24 10:16:52
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-501. 二叉搜索树中的众数](https://leetcode-cn.com/problems/find-mode-in-binary-search-tree/)

**原始思路**

```java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();
    public int[] findMode(TreeNode root) {
        if (root == null) {
            return new int[]{};
        }

        dfs(root);

        //记录最大值
        int max = Integer.MIN_VALUE;
        for(Map.Entry<Integer, Integer> entry: map.entrySet()){
            max = Math.max(max, entry.getValue());
        }

        List<Integer> list = new ArrayList<>();
        for(Map.Entry<Integer, Integer> entry: map.entrySet()){
            if (entry.getValue() == max){
                list.add(entry.getKey());
            }
        }

        int[] arr = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            arr[i] = list.get(i);
        }
        return arr;
    }

    public void dfs(TreeNode root){
        if (root.left != null) {
            dfs(root.left);
        }

        if (root != null) {
            if (map.containsKey(root.val)){
                map.put(root.val, map.get(root.val) + 1);
            }else {
                map.put(root.val, 1);
            }
        }

        if (root.right != null) {
            dfs(root.right);
        }
    }
}
```

