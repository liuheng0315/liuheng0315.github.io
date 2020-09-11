---
title: leetcode-491.递增子序列
date: 2020-09-10 13:24:19
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-491.递增子序列](https://leetcode-cn.com/problems/increasing-subsequences/)

**原始思路**

> ```java
> class Solution {
>     List<List<Integer>> result = new ArrayList<>();
>     List<Integer> ans = new ArrayList<>();
>     Set<String> set = new HashSet<>();
>     public List<List<Integer>> findSubsequences(int[] nums) {
>         dfs(-101, 0 , nums);
>         return result;
>     }
> 
>     public void dfs(int pre, int begin, int[] nums){
>         if (begin > nums.length){
>             return;
>         }
>         
>         if (ans.size() >= 2 && !set.contains(ans.toString())){
>             result.add(new ArrayList<Integer>(ans));
>             set.add(ans.toString());
>         }
> 
>         for (int i = begin; i < nums.length; i++){
>             if (nums[i] < pre){
>                 continue;
>             }
>             ans.add(nums[i]);
>             dfs(nums[i], i + 1, nums);
>             ans.remove(ans.size() - 1);
>         }
>     }
> }
> ```
>

