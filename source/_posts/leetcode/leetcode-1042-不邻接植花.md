---
title: leetcode-1042. 不邻接植花
date: 2020-09-23 16:36:10
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1042. 不邻接植花](https://leetcode-cn.com/problems/flower-planting-with-no-adjacent/)

**原始思路**

```java
class Solution {
    public int[] gardenNoAdj(int N, int[][] paths) {
        int[] result = new int[N];
        if (paths.length == 0) {
            Arrays.fill(result, 1);
            return result;
        }

        Map<Integer, Set<Integer>> map = new HashMap<>();
        for(int[] path: paths){
            // 正向
            if (map.containsKey(path[0])) {
                Set<Integer> curSet = map.get(path[0]);
                curSet.add(path[1]);
                map.put(path[0], curSet);
            }else {
                Set<Integer> set = new HashSet<>();
                set.add(path[1]);
                map.put(path[0], set);
            }

            // 反向
            if (map.containsKey(path[1])) {
                Set<Integer> curSet = map.get(path[1]);
                curSet.add(path[0]);
                map.put(path[1], curSet);
            }else {
                Set<Integer> set = new HashSet<>();
                set.add(path[0]);
                map.put(path[1], set);
            }
        }

        //在1这个院子种上1这个花
        result[0] = 1;
        for (int i = 2; i <= N; i++) {
            // 存放相邻院子已经种上的花
            Set<Integer> aSet = new HashSet<>();
            // 相邻的院子
            Set<Integer> s = map.get(i);
            if (s != null) {
                for (Integer o : s) {
                    if (result[o - 1] != 0) {
                        // 说明已经种上花
                        aSet.add(result[o - 1]);
                    }
                }
            }

            //开始种花
            for (int j = 1; j <= 4; j++) {
                if (!aSet.contains(j)) {
                    result[i-1] = j;
                    break;
                }
            }
        }
        return result;
    }
}
```