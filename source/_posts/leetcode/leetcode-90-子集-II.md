---
title: leetcode-90. 子集 II
date: 2021-03-31 09:04:30
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)

```java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    Set<List<Integer>> set = new HashSet<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        dfs(0, nums, new ArrayList<>());
        return ans;
    }

    public void dfs(int cur, int[] nums, List<Integer> list){
        if (!set.contains(list)){
            List<Integer> copy = new ArrayList<>(list);
            set.add(copy);
            ans.add(copy);
        }
        for (int i = cur; i < nums.length; i++) {
            list.add(nums[i]);
            dfs(i+1, nums, list);
            list.remove(list.size()-1);
        }
    }
}
```

