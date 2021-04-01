---
title: acwing-92. 递归实现指数型枚举
date: 2021-04-01 13:53:27
categories: 
		- 算法
tags: 
	- 递归
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-92. 递归实现指数型枚举](https://www.acwing.com/problem/content/description/94/)

```java
import java.util.*;
public class Main{
    static List<List<Integer>> res = new ArrayList<>();
    
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        dfs(1, n, new ArrayList<>());
        System.out.println("");
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
    
    public static void dfs(int cur, int n, List<Integer> ans){
        
        if(ans.size() > 0 && !res.contains(ans)){
            res.add(new ArrayList<Integer>(ans));
        }
        
        for(int i = cur; i <= n; i++){
            ans.add(i);
            dfs(i + 1, n, ans);
            ans.remove(ans.size()-1);
        }
    }
}
```

