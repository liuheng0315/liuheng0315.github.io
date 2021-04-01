---
title: acwing-94. 递归实现排列型枚举
date: 2021-04-01 17:10:36
categories: 
		- 算法
tags: 
	- 递归
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-94. 递归实现排列型枚举](https://www.acwing.com/problem/content/description/96/)

```java
import java.util.*;
import java.io.*;
public class Main{
    static List<List<Integer>> res = new ArrayList<>();
    static Set<Integer> set = new HashSet<>();
    static BufferedWriter log = new BufferedWriter(new OutputStreamWriter(System.out));
    public static void main(String[] args) throws Exception{
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = n;
        
        dfs(1, n, m, new ArrayList<>());
        
        for(List<Integer> list : res){
            for(int i = 0; i < list.size(); i++){
                log.write(String.valueOf(list.get(i)));
                if(i != list.size()-1){
                    log.write(" ");
                }
            }
            log.write("\n");
        }
        log.flush();
    }
    
    public static void dfs(int cur, int n, int m, List<Integer> ans){
        if(ans.size() == m){
            res.add(new ArrayList<>(ans));
            return;
        }
        
        for(int i = cur; i <= n; i++){
            if(set.contains(i)){
                continue;
            }
            ans.add(i);
            set.add(i);
            dfs(cur, n, m, ans);
            ans.remove(ans.size()-1);
            set.remove(i);
        }
    }
}
```

