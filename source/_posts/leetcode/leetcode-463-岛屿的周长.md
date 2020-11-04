---
title: leetcode-463. 岛屿的周长
date: 2020-10-30 09:18:28
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-463. 岛屿的周长](https://leetcode-cn.com/problems/island-perimeter/)

```java
class Solution {
    int n1 = 0;
    int n2 = 0;
    public int islandPerimeter(int[][] grid) {
        int count = 0;
        n1 = grid.length;
        n2 = grid[0].length;
        for(int i = 0; i < n1; i++){
            for(int j = 0; j < n2; j++){
                if(grid[i][j] == 1){
                    count += getCount(grid, i-1, j) + getCount(grid, i+1, j) + getCount(grid, i, j-1) + getCount(grid, i, j+1);
                }
            }
        }
        return count;
    }

    public int getCount(int[][] grid, int i, int j){
        if(i < 0 || i >= n1 || j <0 || j >=n2){
            return 1;
        }else{
            if(grid[i][j] == 1){
                return 0;
            }else{
                return 1;
            }
        }
    }
}
```

