---
title: leetcode-218. 天际线问题
date: 2023-04-20 22:24:14
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

[leetcode-218. 天际线问题](https://leetcode.cn/problems/the-skyline-problem/)

```java
import java.util.*;
class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        List<List<Integer>> res = new ArrayList<>();

        if(buildings == null || buildings.length == 0){
            return res;
        }

        // 采用扫描线方案解决
        // 定义一个存放最大值的优先队列
        Queue<int[]> queue = new PriorityQueue<>(buildings.length, (o1,o2)->{
            int height1 = o1[2];
            int height2 = o2[2];
            // 倒序 最高排第一
            return height2 - height1;
        });

        // 定义扫描线
        List<Integer> list = new ArrayList<>();
        Map<Integer, List<int[]>> map = new HashMap<>();

        // 将数据读入队列
        for(int[] arr : buildings){
            int x1 = arr[0];
            int x2 = arr[1];
            int h = arr[2];
        
            if(!list.contains(x1)){
                list.add(x1);
            }

            if(!list.contains(x2)){
                list.add(x2);
            }
            List<int[]> list1 = map.getOrDefault(x1, new ArrayList<>());
            list1.add(arr);
            map.put(x1, list1);

            List<int[]> list2 = map.getOrDefault(x2, new ArrayList<>());
            list2.add(arr);
            map.put(x2, list2);
        }

        Collections.sort(list);
        
        // 前一个点的高度
        int last = 0;

        // 遍历扫描线
        for(Integer x : list){
            List<int[]> arrList = map.get(x);
            for(int[] arr : arrList){
                if(arr[0] == x){
                    // 加入队列
                    queue.offer(arr);
                }else if(arr[1] == x){
                    // 移除队列
                    queue.remove(arr);
                }
            }
            
            if(queue.size() == 0){
                // 队列已经全部移除
                List<Integer> ans = new ArrayList<>();
                ans.add(x);
                ans.add(0);
                res.add(ans);
                last = 0;
                continue;
            }

            // 此时队列中的最大值
            int[] p = queue.peek();

            if(p[2] != last){

                last = p[2];

                List<Integer> ans = new ArrayList<>();
                ans.add(x);
                ans.add(p[2]);
                res.add(ans);
            }
        }

        return res;
    }
}
```

