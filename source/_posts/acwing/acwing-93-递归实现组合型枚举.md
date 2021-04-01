---
title: acwing-93. 递归实现组合型枚举
date: 2021-04-01 14:55:34
categories: 
		- 算法
tags: 
	- 递归
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-93. 递归实现组合型枚举](https://www.acwing.com/problem/content/95/)

```java
import java.util.*;
public class Main{
    static List<List<Integer>> res = new ArrayList<>();
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        
        dfs(1, n, m, new ArrayList<>());
        
        for(List<Integer> list : res){
            for(int i = 0; i < list.size(); i++){
                System.out.print(list.get(i));
                if(i != list.size()-1){
                    System.out.print(" ");
                }
            }
            System.out.println("");
        }
    }
    
    public static void dfs(int cur, int n, int m, List<Integer> ans){
        if(ans.size() == m && !res.contains(ans)){
            res.add(new ArrayList<>(ans));
            return;
        }
        
        for(int i = cur; i <= n; i++){
            ans.add(i);
            dfs(i+1, n, m, ans);
            ans.remove(ans.size()-1);
        }
    }
}
```

