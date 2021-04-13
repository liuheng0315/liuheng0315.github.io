---
title: acwing-835. Trie字符串统计
date: 2020-10-20 13:58:15
categories: 
		- algorithm
tags: 
	- Trie树
cover: /images/leetcode.jpg
typora-root-url: ..
---

[AcWing 835. Trie字符串统计](https://www.acwing.com/solution/content/20915/)

```java
import java.util.Scanner;

public class Main {
    private static int N=100010;
    private static int son[][]=new int[N][26];//Trie树中每个节点的所有儿子
    private static int cnt[]=new int[N];//以当前这个点结尾的单词有多少个
    private static char str[]=new char[N];
    private static int idx;//当前用的的哪个下标，下标0：既是根节点又是空节点

    public static void main(String[] args){
        Scanner scanner=new Scanner(System.in);
        int n=scanner.nextInt();
        while (n-->0){
            String op=scanner.next();
            String str=scanner.next();
            if("I".equals(op)){
                insert(str.toCharArray());
            }else if("Q".equals(op)){
                System.out.println(query(str.toCharArray()));
            }
        }
    }

    //插入
    private static void insert(char[] str) {
        int p=0;//根节点是0
        //从根节点开始，从前向后遍历字符串
        for(int i=0;i<str.length ;i++){
            int u=str[i]-'a';//把当前这个字母对应的子节点编号搞出来
            //如果当前这个点上不存在对应的字母的话，创建出来
            if(son[p][u]==0) son[p][u]=++idx;
            p=son[p][u];//走到下一个点
        }
        cnt[p]++;//p对应的点就是最后一个点，cnt[p]++表示以这个点结尾的单词数量加1
    }

    //查找字符串出现的次数
    private static int query(char[] str) {
        int p=0;
        for(int i=0;i<str.length;i++){
            int u=str[i]-'a';
            //如果不存在这个子节点的话，说明集合中不存在这个单词
            if(son[p][u]==0) return 0;
            //否则的话走过去
            p=son[p][u];
        }
        return cnt[p];//返回以p结尾的单词数量
    }
}
```

