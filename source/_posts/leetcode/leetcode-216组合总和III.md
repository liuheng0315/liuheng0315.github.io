---
title: leetcode-216.组合总和III
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

[leetcode-216.组合总和III](https://leetcode-cn.com/problems/combination-sum-iii/)

```java
class Solution {
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> ans = new ArrayList<>();
    public List<List<Integer>> combinationSum3(int k, int n) {
        int[] arr = new int[]{1,2,3,4,5,6,7,8,9};
        dfs(arr, 0, k, n);
        return result;
    }

    public void dfs(int[] arr, int begin, int k, int n){
        if (n == 0 && k == 0){
            result.add(new ArrayList<Integer>(ans));
            return;
        }

        if (k <= 0){
            return;
        }

        for (int i = begin; i < arr.length; i++) {
            ans.add(arr[i]);
            dfs(arr, i + 1, k - 1, n - arr[i]);
            ans.remove(ans.size() - 1);
        }
    }
}
```

