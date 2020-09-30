---
title: leetcode-947. 移除最多的同行或同列石头
date: 2020-09-29 18:01:21
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-947. 移除最多的同行或同列石头](https://leetcode-cn.com/problems/most-stones-removed-with-same-row-or-column/)

```java
class Solution {
    public int removeStones(int[][] stones) {
        int N = stones.length;
        int[] parent = new int[N];
        for (int i = 0; i < N; i++) {
            parent[i] = i;
        }

        // 数据读入
        for (int i = 0; i < stones.length; i++) {
            int[] arr = stones[i];
            int x = arr[0];
            int y = arr[1];
            for (int j = i + 1; j < stones.length; j++) {
                int[] arr2 = stones[j];
                int x2 = arr2[0];
                int y2 = arr2[1];
                if (x == x2 || y == y2) {
                    //证明在同行或者同列
                    unoin(i, j, parent);
                }
            }
        }

        int result = 0;
        for (int i = 0; i < parent.length; i++) {
            if (parent[i] == i) {
                //存在一个根节点为i的根
                int count = 0;
                //遍历这个圈中有多少个元素
                for (int j = 0; j < parent.length; j++) {
                    if (find(j, parent) == i) {
                        count++;
                    }
                }

                if (count > 1) {
                    result += count - 1;
                }
            }
        }

        return result;
    }

    public int find(int j, int[] parent) {
        if (j != parent[j]) {
            parent[j] = find(parent[j], parent);
        }
        return parent[j];
    }

    public void unoin(int i, int j, int[] parent) {
        int x = find(i, parent);
        int y = find(j, parent);
        if (x != y) {
            parent[x] = y;
        }
    }
}
```

