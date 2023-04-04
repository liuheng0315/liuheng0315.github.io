---
title: leetcode-443. 压缩字符串
date: 2021-07-23 17:42:13
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [443. 压缩字符串](https://leetcode-cn.com/problems/string-compression/)

```java
class Solution {
    public int compress(char[] chars) {
        int n = chars.length;
        int[] parent = new int[n];
        for(int i = 0; i < n; i++){
            parent[i] = i;
        }
        
        for(int i = 1; i < n; i++){
            char ch = chars[i];
            if(ch == chars[i-1]){
                union(i, i-1, parent);
            }
        }

        StringBuffer sb = new StringBuffer();
        int count = 1;
        sb.append(chars[0]);
        for(int i = 1; i < n; i++){
            int pre = find(i-1, parent);
            int p1 = find(i, parent);
            if(pre == p1){
                count++;
            }else{
                if(count != 1){
                    sb.append(count +"");
                    count = 1;
                }
                sb.append(chars[i] +"");
            }
        }

        if(count != 1){
            sb.append(count +"");
            count = 0;
        }

        for(int i = 0; i < sb.length(); i++){
            chars[i] = sb.charAt(i);
        }
        return sb.length();
    }

    public int find(int k, int[] parent){
        if(k != parent[k]){
            parent[k] = find(parent[k], parent);
        }
        return parent[k];
    }

    public void union(int i, int j, int[] parent){
        int x = find(i, parent);
        int y = find(j, parent);
        if(x != y){
            parent[x] = y;
        }
    }
}
```

