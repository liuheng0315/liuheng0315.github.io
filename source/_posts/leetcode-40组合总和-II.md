---
title: leetcode-40.组合总和 II
date: 2020-09-16 09:16:49
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-40.组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

**原始思路**

```java
class Solution{
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();
    int total = 0;
    Set<String> set = new HashSet<>();
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(0, candidates, target);
        return result;
    }
    
    public void dfs(int begin, int[] candidates, int target){
        if(target == 0){
            //符合条件，加入数据
            List<Integer> ans = new ArrayList<>(tmp);
            if(!set.contains(ans.toString())){
                set.add(ans.toString());
                result.add(ans);
            }
            
            return;
        }
        for(int i = begin; i < candidates.length; i++){
            // 进行剪枝,前提是数组有序
            if(target - candidates[i] < 0){
                break;
            }
            tmp.add(candidates[i]);
            dfs(i + 1, candidates, target - candidates[i]);
            tmp.remove(tmp.size() - 1);
        }
    }
}
```

