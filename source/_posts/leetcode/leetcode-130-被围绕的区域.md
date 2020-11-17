---
title: leetcode-130. 被围绕的区域
date: 2020-09-29 16:35:26
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

**原始思路：并查集**

```java
class Solution {
    public void solve(char[][] board) {
        int n1 = board.length;
        if (n1 == 0) {
            return;
        }
        int n2 = board[0].length;

        int[] parent = new int[n1 * n2];
        // 初始化并查集
        for (int i = 0; i < parent.length; i++) {
            parent[i] = i;
        }

        for (int i = 0; i < n1-1; i++) {
            for (int j = 1; j < n2; j++) {
                if (board[i][j] == 'O' && board[i][j - 1] == board[i][j]) {
                    union(i * n2 + j, i * n2 + j - 1, parent);
                }
                if (board[i][j] == 'O' && board[i+1][j] == board[i][j]) {
                    union(i * n2 + j, (i+1) * n2 + j, parent);
                }
            }
        }

        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < parent.length; i++) {
            int x = i / n2;
            int y = i - x * n2;
            if (i == parent[i] && board[x][y] == 'O') {
                list.add(i);
            }
        }

        for (int i = 0; i < list.size(); i++) {
            boolean b = true;
            for (int j = 0; j < parent.length; j++) {
                int x = j / n2;
                int y = j - x * n2;
                if (find(j, parent) == list.get(i) && (x == 0 || x == n1 - 1 || y == 0 || y == n2 - 1)) {
                    b = false;
                }
            }

            if (b) {
                for (int j = 0; j < parent.length; j++) {
                    int x = j / n2;
                    int y = j - x * n2;
                    if (find(j, parent) == list.get(i)) {
                        board[x][y] ='X';
                    }
                }
            }
        }

    }

    public int find(int k, int[] parent) {
        if (parent[k] != k) {
            parent[k] = find(parent[k], parent);
        }
        return parent[k];
    }

    public void union(int i, int j, int[] parent) {
        int x = find(i, parent);
        int y = find(j, parent);
        if (x != y) {
            parent[x] = y;
        }
    }
}
```

