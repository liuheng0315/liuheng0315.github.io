---
title: leetcode-1535. 找出数组游戏的赢家
date: 2020-09-25 17:36:24
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1535. 找出数组游戏的赢家](https://leetcode-cn.com/problems/find-the-winner-of-an-array-game/)

**原始思路**

```java
class Solution {
    Map<Integer, Integer> map = new HashMap<>();
    public int getWinner(int[] arr, int k) {
        LinkedList<Integer> list = new LinkedList<>();
        int max = -100000000;
        for (int i = 0; i < arr.length; i++) {
            list.add(arr[i]);
            max = Math.max(max, arr[i]);
        }

        boolean bool = true;
        int result = -100000000;
        while (bool) {
            int i = list.get(0);
            int j = list.get(1);
            if (i == max || j == max) {
                return max;
            }
            if (i > j) {
                exchange(list, 1);
                if (map.getOrDefault(i, 0) + 1 == k) {
                    bool = false;
                    result = i;
                }
                map.put(i, map.getOrDefault(i, 0) + 1);
            }else {
                exchange(list, 0);
                if (map.getOrDefault(j, 0) + 1 == k) {
                    bool = false;
                    result = j;
                }
                map.put(j, map.getOrDefault(j, 0) + 1);
            }
        }
        return result;
    }

    public void exchange(LinkedList<Integer> list, int index){
        //将较小的数移动至末尾
        int i = list.pollFirst();
        int j = list.pollFirst();
        if (index == 0) {
            list.offerLast(i);
            list.offerFirst(j);
        }else {
            list.offerLast(j);
            list.offerFirst(i);
        }
    }
}
```

