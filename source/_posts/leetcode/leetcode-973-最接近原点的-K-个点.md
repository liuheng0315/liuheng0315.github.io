---
title: leetcode-973. 最接近原点的 K 个点
date: 2020-11-09 09:06:31
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-973. 最接近原点的 K 个点](https://leetcode-cn.com/problems/k-closest-points-to-origin/)

```java
class Solution {
    public int[][] kClosest(int[][] points, int K) {
        PriorityQueue<int[]> queue = new PriorityQueue<>((o1, o2) -> {
           Double dist1 = Math.sqrt(o1[0]*o1[0] + o1[1]*o1[1]);
           Double dist2 = Math.sqrt(o2[0]*o2[0] + o2[1]*o2[1]);
            return dist1.compareTo(dist2); 
        });

        for(int[] arr : points){
            queue.offer(arr);
        }

        int[][] ans = new int[K][2];
        int i = 0;
        while (K > 0) {
            int[] tmp = queue.poll();
            ans[i++] = tmp;
            K--;
        }
        return ans;
    }
}
```

