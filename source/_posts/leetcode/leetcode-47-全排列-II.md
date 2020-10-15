---
title: leetcode-47. 全排列 II
date: 2020-09-21 09:16:49
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

**原始思路**

```java
class Solution {
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> ans = new ArrayList<>();
    Set<Integer> set = new HashSet<>();
    Set<List<Integer>> setAns = new HashSet<>();
    public List<List<Integer>> permuteUnique(int[] nums) {
        dfs(0, nums);
        return result;
    }

    public void dfs(int cur, int[] nums){
        if(cur >= nums.length){
            return;
        }

        if(ans.size() == nums.length){
            if(!setAns.contains(ans)){
                setAns.add(ans);
                result.add(new ArrayList(ans));
            }
            return;
        }

        for(int i = cur; i < nums.length; i++){
            if(set.contains(i)){
                continue;
            }
            set.add(i);
            ans.add(nums[i]);
            dfs(cur, nums);
            ans.remove(ans.size() -1);
            set.remove(i);
        }
    }
}
```

