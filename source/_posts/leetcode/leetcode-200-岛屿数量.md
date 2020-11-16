---
title: leetcode-200. 岛屿数量
date: 2020-09-22 17:16:54
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: .
---

#### [leetcode-200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

**原始思路**

```java
class Solution {
    public int numIslands(char[][] grid) {
        int n1 = grid.length;
        if (n1 == 0) {
            return 0;
        }
        int n2 = grid[0].length;
        
        // 定义并查集的parent
        int[] parent = new int[n1*n2];
        // 初始化并查集
        for (int i = 0; i < n1*n2; i++) {
            parent[i] = i;
        }

        //遍历矩阵
        for (int i = 0; i <= n1 - 1; i++) {
            for (int j = 0; j <= n2 - 1; j++) {
                if (j!= n2-1 && grid[i][j] == grid[i][j + 1] && grid[i][j] == '1'){
                    //两个列上的数字是联通的
                    union(parent, i * n2 + j, i * n2 + j + 1);
                }

                if (i!= n1-1 && grid[i][j] == grid[i+1][j] && grid[i][j] == '1') {
                    //两个行上的数字是联通的
                    union(parent, i * n2 + j, (i+1) * n2 + j);
                }
            }
        }

        // 遍历parent
        int result = 0;
        for (int i = 0; i < n1; i++) {
            for (int j = 0; j < n2; j++) {
                if (grid[i][j] == '1' && parent[i * n2 + j] == i * n2 + j) {
                    result++;
                }
            }
        }
        return result;
    }


    // 并查集查找
    public int find(int[] parent, int i) {
        if (parent[i] != i) {
            parent[i] = find(parent, parent[i]);
        }
        return parent[i];
    }

    //并查集合并
    public void union(int[] parent, int i, int j) {
        int x = find(parent, i);
        int y = find(parent, j);
        if (x != y) {
            parent[x] = y;
        }
    }
}
```

