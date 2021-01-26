---
title: acwing-245. 你能回答这些问题吗
date: 2021-01-26 13:48:04
categories: 
		- 算法
tags: 
	- 线段树
cover: /images/leetcode.jpg
typora-root-url: ..
---

[acwing-245. 你能回答这些问题吗](https://www.acwing.com/problem/content/description/246/)

```java
import java.io.*;
class Main{
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
    static Node[] tr;
    static int[] A;
    public static void main(String[] agrs) throws Exception{
        String[] strs = br.readLine().split(" ");
        int n = Integer.valueOf(strs[0]);
        int m = Integer.valueOf(strs[1]);
        tr = new Node[4*n];
        strs = br.readLine().split(" ");
        A = new int[n+1];
        for(int i = 1; i <= n; i++){
            A[i] = Integer.valueOf(strs[i-1]);
        }
        
        // 构建线段树
        build(1, 1, n);
        
        while(m-- > 0){
            strs = br.readLine().split(" ");
            int k = Integer.valueOf(strs[0]);
            int x = Integer.valueOf(strs[1]);
            int y = Integer.valueOf(strs[2]);
            
            if(k == 1){
                if(x>y){
					int tmp = 0;
					tmp = x;
                    x = y;
					y = tmp;
                }
                Node node = query(1, x, y);
                bw.write(node.tmax + "\n");
            }else{
                modify(1, x, y);
            }
        }
        bw.flush();
    }
    
    static void modify(int u, int x, int c){
        if(tr[u].l == x && tr[u].r == x){
            tr[u].sum = c;
            tr[u].lmax = c;
            tr[u].rmax = c;
            tr[u].tmax = c;
        }else{
            int mid = (tr[u].l + tr[u].r) >> 1;
            if(x <= mid){
                // 在左边
                modify(u << 1, x, c);
            }else{
                // 在右边
                modify(u << 1|1, x, c);
            }
            
            // 执行pushUp操作
            pushUp(u);
        }
    }
    
    static Node query(int u, int l, int r){
        if(tr[u].l >= l && tr[u].r <= r){
            return tr[u];
        }
        
        int mid = (tr[u].l+tr[u].r) >> 1;
        if(r<= mid){
            // 说明在左边
            return query(u << 1, l, r);
        }else if(l > mid){
            // 说明在右边
            return query(u << 1|1, l, r);
        }else{
            // 说明在两边
            Node left = query(u << 1, l, r);
            Node right = query(u << 1|1, l, r);
            Node root = new Node(l, r);
            pushUp(root, left, right);
            return root;
        }
    }
    
    static void build(int u, int l, int r){
        if(l == r){
            tr[u] = new Node(l, r, A[l], A[l], A[l], A[l]);
        }else{
            tr[u] = new Node(l, r);
            int mid = (l + r) >> 1;
            build(u << 1, l , mid);
            build(u << 1 | 1, mid + 1, r);
            pushUp(u);
        }
    }
    
    static void pushUp(int u){
        pushUp(tr[u], tr[u<<1], tr[u<<1|1]);
    }
    
    static void pushUp(Node root, Node l, Node r){
        root.sum = l.sum + r.sum;
        root.lmax = Math.max(l.lmax, l.sum + r.lmax);
        root.rmax = Math.max(r.rmax, r.sum + l.rmax);
        root.tmax = Math.max(Math.max(l.tmax, r.tmax), l.rmax + r.lmax);
    }
    
    static class Node{
        int l;
        int r;
        int sum;
        int lmax;
        int rmax;
        int tmax;
        Node(int l, int r){
            this.l = l;
            this.r = r;
        }
        
        Node(int l, int r, int sum, int lmax, int rmax, int tmax){
            this.l = l;
            this.r = r;
            this.sum = sum;
            this.lmax = lmax;
            this.rmax = rmax;
            this.tmax = tmax;
        }
    }
}
```

