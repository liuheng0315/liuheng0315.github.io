---
title: leetcode-547.朋友圈
date: 2020-09-10 17:01:16
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-547.朋友圈](https://leetcode-cn.com/problems/friend-circles/)

```java
class Solution {
    public int findCircleNum(int[][] M) {
        // 初始化存储父节点
        int[] parent = new int[M.length];
        // 将所有的数的parent均初始化为-1,表示自己指向自己
        for (int i = 0; i < parent.length; i++) {
            parent[i] = i;
        }

        //遍历矩阵
        for (int i = 0; i < M.length ; i++) {
            for (int j = 0; j < M.length ; j++) {
                if (M[i][j] == 1 && i != j){
                    //说明两个不同的数字是相联通的
                    // 此时将两个归为一个朋友圈
                    union(parent, i, j);
                }
            }
        }

        //此时遍历parent数组
        int count = 0;
        for (int i = 0; i < parent.length; i++) {
            if (parent[i] == i){
                count++;
            }
        }
        return count;
    }

    //并查集 找到父节点,未压缩
    public int find(int[] parent, int i){
        if (parent[i] == i){
            return i;
        }
        return find(parent, parent[i]);
    }

    // 并查集,查询父节点，并压缩，当第一次查完，以后再查就是近乎于O(1)的时间复杂度
    public int findRoot(int[] parent, int i){
        if (parent[i] != i){
            parent[i] = findRoot(parent, parent[i]);
        }
        return parent[i];
    }

    //合并两个数
    public void union(int[] parent, int i, int j){
        int xset = findRoot(parent, i);
        int yset = findRoot(parent, j);
        if (xset != yset){
            parent[xset] = yset;
        }
    }
}
```

