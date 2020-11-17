---
title: leetcode-997. 找到小镇的法官
date: 2020-09-23 14:22:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-997. 找到小镇的法官](https://leetcode-cn.com/problems/find-the-town-judge/)

**原始思路**

```java
class Solution {
    public int findJudge(int N, int[][] trust) {
        // 首先找出谁都不信任的人
        int n1 = trust.length;
        Map<Integer, List<Integer>> map = new HashMap<>();
        for (int i = 0; i < n1; i++) {
            int[] arr = trust[i];
            if (map.containsKey(arr[0])){
                List<Integer> curList = map.get(arr[0]);
                curList.add(arr[1]);
                map.put(arr[0], curList);
            }else {
                List<Integer> list = new ArrayList<>();
                list.add(arr[1]);
                map.put(arr[0], list);
            }
        }

        if (map.size() != N - 1) {
            // 存在多个谁都不信任的人或者一个都不存在
            return -1;
        }

        // 找出这个人
        int k = -1;
        for (int i = 1; i <= N; i++) {
            if (!map.containsKey(i)) {
                k = i;
                break;
            }
        }

        //判断是否所有人都信任他
        boolean b = true;
        for (int i = 1; i <= N; i++) {
            if (i == k) {
                continue;
            }

            if (!map.get(i).contains(k)) {
                b = false;
                break;
            }
        }

        return b ? k : -1;
    }
}
```



**题解思路**

```java
class Solution {
    public int findJudge(int N, int[][] trust) {
        // 统计出度和入度
        int[] in = new int[N+1];
        int[] out = new int[N+1];
        
        for(int[] arr: trust){
            in[arr[1]] += 1;
            out[arr[0]] += 1;
        }
        
        // 循环遍历结果，看是否只存在一个法官
        boolean b = false;
        int result = -1;
        for(int i = 1; i <= N; i++){
            if(in[i] == N - 1 && out[i] == 0){
                if(b){
                    b = false;
                    break;
                }
                b = true;
                result = i;
            }
        }
        return b ? result : -1;
    }
}
```

