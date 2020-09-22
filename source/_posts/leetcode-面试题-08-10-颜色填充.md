---
title: leetcode-面试题 08.10. 颜色填充
date: 2020-09-22 14:32:17
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-面试题 08.10. 颜色填充](https://leetcode-cn.com/problems/color-fill-lcci/)

**原始思路**

```java
class Solution {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int n1 = image.length;
        int n2 = image[0].length;
        int[][] visit = new int[n1][n2];
        int origin = image[sr][sc];
        dfs(image, sr, sc, newColor, n1, n2, visit, origin);
        return image;
    }

    public void dfs(int[][] image, int sr, int sc, int newColor, int n1, int n2, int[][] visit, int origin) {
        // 判断边界条件
        if (sr < 0 || sr >= n1 || sc < 0 || sc >= n2) {
            return;
        }

        // 为0证明不通了
        if (image[sr][sc] != origin) {
            return;
        }

        //如果是已经访问过的,则直接返回
        if (visit[sr][sc] == 1) {
            return;
        }

        // 更改颜色
        image[sr][sc] = newColor;

        // 记录该位置是否被访问过
        visit[sr][sc] = 1;

        dfs(image, sr - 1, sc, newColor, n1, n2, visit, origin);
        dfs(image, sr + 1, sc, newColor, n1, n2, visit, origin);
        dfs(image, sr, sc - 1, newColor, n1, n2, visit, origin);
        dfs(image, sr, sc + 1, newColor, n1, n2, visit, origin);
    }
}
```

