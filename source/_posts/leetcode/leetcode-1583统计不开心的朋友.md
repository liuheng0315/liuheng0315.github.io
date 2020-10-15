---
title: leetcode-1583.统计不开心的朋友
date: 2020-09-15 14:36:31
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1583. 统计不开心的朋友](https://leetcode-cn.com/problems/count-unhappy-friends/)

**原始思路**

```java
class Solution {
    int count = 0;
    public int unhappyFriends(int n, int[][] preferences, int[][] pairs) {
        for (int i = 0; i < n/2; i++) {
            //一对对进行判断是否亲密
            isUnHappy(n, preferences, pairs, i, 0);
            isUnHappy(n, preferences, pairs, i, 1);
        }
        return count;
    }

    public void isUnHappy(int n, int[][] preferences, int[][] pairs, int i, int j){
        // 判断朋友的亲密性
        int self = pairs[i][j];

        // 配对的朋友
        int friend;
        if (j == 0) {
            friend = pairs[i][1];
        }else {
            friend = pairs[i][0];
        }

        // 自己的朋友有哪些
        // 记录比匹配的朋友更亲密的朋友
        // 把比匹配到亲密度更高的朋友加入到集合中,这些数都比匹配的朋友更亲密
        List<Integer> selfMoreSweetFriend = findMoreSweetFriends(n, preferences, self, friend);

        //遍历看匹配的朋友,朋友的匹配朋友的亲密度是否比self低
        for (int k = 0; k < selfMoreSweetFriend.size() ; k++) {
            // 查找出更亲密的朋友
            int sweet = selfMoreSweetFriend.get(k);
            int matchFriend = findFriend(pairs, sweet);
            List<Integer> matchMoreSweetFriend = findMoreSweetFriends(n, preferences, sweet, matchFriend);
            if (matchMoreSweetFriend.contains(self)){
                // 则认为是不开心
                count++;
                break;
            }
        }
    }

    public List<Integer> findMoreSweetFriends(int n, int[][] preferences, int self, int matchFriend){
        List<Integer> list = new ArrayList<Integer>();
        for (int k = 0; k < n - 1; k++) {
            if (preferences[self][k] == matchFriend) {
                //匹配到亲密度的朋友
                break;
            }
            list.add(preferences[self][k]);
        }
        return list;
    }

    public int findFriend(int[][] pairs, int sweet){
        for (int i = 0; i < pairs.length; i++) {
            if (pairs[i][0] == sweet) {
                return pairs[i][1];
            }
            if (pairs[i][1] == sweet) {
                return pairs[i][0];
            }
        }
        return -1;
    }
}
```

