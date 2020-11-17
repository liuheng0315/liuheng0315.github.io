---
title: leetcode-841. 钥匙和房间
date: 2020-09-29 10:32:07
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

**原始思路**

```java
class Solution {
    Set<Integer> set = new HashSet<>();
    boolean[] st;

    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        int n = rooms.size();
        st = new boolean[n];
        set.add(0);
        return dfs(0, n, rooms);
    }

    public boolean dfs(int cur, int n, List<List<Integer>> rooms) {
        if (set.size() == n) {
            // 拥有所有钥匙
            return true;
        }

        st[cur] = true;
        for (int i = 0; i < rooms.get(cur).size(); i++) {
            set.add(rooms.get(cur).get(i));
        }

        for(Integer k : set) {
            if (!st[k]) {
                return dfs(k, n, rooms);
            }
        }

        return false;
    }
}
```



**题解DFS**

```java
class Solution {
    int num = 0;
    boolean[] st;

    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        int n = rooms.size();
        st = new boolean[n];
        dfs(0, rooms);
        return num == n;
    }

    public void dfs(int cur, List<List<Integer>> rooms) {
        num++;
        st[cur] = true;
        for (int i = 0; i < rooms.get(cur).size(); i++) {
            if(!st[rooms.get(cur).get(i)]){
                dfs(rooms.get(cur).get(i), rooms);
            }
        }
    }
}
```



**题解BFS**

```java
class Solution {
    int num = 0;
    boolean[] st;
	LinkedList<Integer> queue = new LinkedList<>();
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        int n = rooms.size();
        st = new boolean[n];
        queue.offer(0);
        st[0] = true;
        bfs(0, rooms);
        return num == n;
    }

    public void bfs(int cur, List<List<Integer>> rooms) {
      	while(!queue.isEmpty()){
            num++;
            int n = queue.size();
            for(int i = 0; i < n; i++){
                int k = queue.poll();
                for(Integer j : rooms.get(k)){
                    if(!st[j]){
                        st[j] = true;
                        queue.add(j);
                    }
                }
            }
        }
    }
}
```

