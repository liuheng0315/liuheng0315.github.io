---
title: leetcode-39.组合总和
date: 2020-09-09 13:12:21
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

**原始思路**

```java
class Solution {
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();
    int total = 0;
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(0, candidates, target);
        return result;
    }

    public boolean dfs(int k, int[] candidates, int target){
        if (total == target){
            result.add(new ArrayList<Integer>(tmp));
            return false;
        }

        if ((target - total)< candidates[0] || k >= candidates.length || total > target){
            return false;
        }

        tmp.add(candidates[k]);
        total += candidates[k];
        if(!dfs(k, candidates, target)){
            k++;
        }
        total -= tmp.get(tmp.size() - 1);
        tmp.remove(tmp.size() - 1);
        if(!dfs(k, candidates, target)){
            k++;
        }
        return false;
    }
}
```



**题解思路**

```java
class Solution{
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> tmp = new ArrayList<>();
    int total = 0;
    Set<String> set = new HashSet<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(0, candidates, target);
        return result;
    }
    
    public void dfs(int begin, int[] candidates, int target){
        if(target == 0){
            //符合条件，加入数据
            result.add(new ArrayList<Integer>(tmp));
            return;
        }
        for(int i = begin; i < candidates.length; i++){
            // 进行剪枝,前提是数组有序
            if(target - candidates[i] < 0){
                break;
            }
            tmp.add(candidates[i]);
            dfs(i, candidates, target - candidates[i]);
            tmp.remove(tmp.size() - 1);
        }
    }
}
```

