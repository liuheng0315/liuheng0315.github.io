---
title: leetcode-452. 用最少数量的箭引爆气球
date: 2020-11-24 09:08:18
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-452. 用最少数量的箭引爆气球](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/)

```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        PriorityQueue<int[]> queue = new PriorityQueue<>(((o1, o2) -> {
            return Integer.compare(o1[0], o2[0]);
        }));

        for(int[] point : points){
            queue.offer(point);
        }

        int[] arr = null;
        int count = 0;
        while (!queue.isEmpty()){
            if (arr == null){
                count ++;
                arr = queue.poll();
            }else {
                // 进行区间缩小
                int[] point = queue.poll();
                if (arr[1] >= point[0]){
                    // 有重合区间,此时缩短区间
                    arr[0] = point[0];
                    arr[1] = Math.min(arr[1], point[1]);
                }else {
                    count ++;
                    arr = point;
                }
            }
        }
        return count;
    }
}
```

