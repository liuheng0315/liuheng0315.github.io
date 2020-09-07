---
title: leetcode-1560圆形赛道上经过次数最多的扇区
date: 2020-09-07 18:27:01
tags:
---

[1560. 圆形赛道上经过次数最多的扇区](https://leetcode-cn.com/problems/most-visited-sector-in-a-circular-track/)

**原始思路**

```java
class Solution {
    public List<Integer> mostVisited(int n, int[] rounds) {
        return mostVisit(n, rounds);
    }

    public List<Integer> mostVisit(int n, int[] rounds){
        //存放每个数字被经过的次数
        HashMap<Integer, Integer> map = new HashMap<>();
        int[] arr = new int[n];
        int[] result = new int[n];
        for (int i = 1; i <= n; i++) {
            arr[i - 1] = i;
        }
        //遍历每一轮
        for (int i = 0; i < rounds.length -1 ; i++) {
            if (i == 0){
                visit(arr, rounds[i], rounds[i + 1], map);
            }else {
                if (rounds[i] == n){
                    visit(arr, arr[0], rounds[i + 1], map);
                }else {
                    visit(arr, arr[rounds[i]], rounds[i + 1], map);
                }
            }
        }

        ArrayList<Integer> list = new ArrayList<>();
        Integer num = 0;
        Integer max = 0;
        for(Map.Entry<Integer, Integer> entry : map.entrySet()){
            if (entry.getValue() > max){
                max = entry.getValue();
                num = entry.getKey();
            }
        }

        for(Map.Entry<Integer, Integer> entry : map.entrySet()){
            if (entry.getValue() == max){
                list.add(entry.getKey());
            }
        }
        Collections.sort(list);
        return list;
    }

    public void visit(int[] arr, int start, int end, HashMap<Integer, Integer> map,){
        //每一轮遍历的数字
        if (start >= end){
            //先从start遍历到数组末尾,然后从数组开头遍历到end
            for (int i = start - 1; i < arr.length; i++) {
                map.put(arr[i], map.getOrDefault(arr[i],0) + 1);
            }
            for (int i = 0; i < end; i++){
                map.put(arr[i], map.getOrDefault(arr[i],0) + 1);
            }
        }else {
            //直接从start遍历到end
            for (int i = start - 1; i < end; i++) {
                map.put(arr[i], map.getOrDefault(arr[i],0) + 1);
            }
        }
    }

}
```

