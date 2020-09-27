---
title: leetcode-1534. 统计好三元组
date: 2020-09-25 16:40:03
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1534. 统计好三元组](https://leetcode-cn.com/problems/count-good-triplets/)

**原始思路**

```java
class Solution {
    List<Integer> list = new ArrayList<>();
    List<List<Integer>> result = new ArrayList<>();
    public int countGoodTriplets(int[] arr, int a, int b, int c) {
        dfs(0, arr, a, b, c);
        return result.size();
    }

    public void dfs(int cur, int[] arr, int a, int b, int c){
        if (list.size() == 3){
            int i = list.get(0);
            int j = list.get(1);
            int k = list.get(2);
            if (Math.abs(i - j) <= a && Math.abs(j - k) <= b && Math.abs(i - k) <= c) {
                result.add(new ArrayList(list));
            }
            return;
        }

        for (int i = cur; i < arr.length; i++) {
            list.add(arr[i]);
            dfs(i + 1, arr, a, b, c);
            list.remove(list.size() - 1);
        }
    }
}
```

