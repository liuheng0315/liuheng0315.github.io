---
title: leetcode-77.组合
date: 2020-09-08 20:05:12
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-77. 组合](https://leetcode-cn.com/problems/combinations/)

```java
class Solution{
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> list = new ArrayList<>();
    public List<List<Integer>> combine(int n, int k){
        dfs(1, n, k);
        return result;
    }
    
    // 使用DFS加回溯的思路
    public void dfs(int cur, int n, int k){
        if((list.size() + n - cur + 1) < k){
            return;
        }
        
        if(list.size() == k){
            result.add(new ArrayList<Integer>(list));
            return;
        }
        // 向list中添加元素
        list.add(cur);
        dfs(cur + 1, n, k);
        //移除元素
        list.remove(list.size() - 1);
        dfs(cur + 1, n , k);
    }
}
```

