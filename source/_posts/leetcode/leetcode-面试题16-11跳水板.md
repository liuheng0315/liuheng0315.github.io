---
title: leetcode-面试题16.11跳水板
date: 2020-09-11 12:15:31
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-面试题16.11跳水板](https://leetcode-cn.com/problems/diving-board-lcci/)

**原始思路**

```java
class Solution {
    public int[] divingBoard(int shorter, int longer, int k) {
        if(k == 0){
            return new int[0];
        }

        List<Integer> result = new ArrayList<>();
               HashSet<Integer> set = new HashSet<>();
        for(int i = 0; i <= k; i++){
            int num = shorter*i+longer*(k - i);
            if(!set.contains(num)){
                result.add(num);
                set.add(num);
            }
        }
        
        Collections.sort(result);
        int[] ans = new int[result.size()];
        for (int i = 0; i <result.size() ; i++) {
            ans[i] = result.get(i);
        }
        return ans;
    }
}
```

