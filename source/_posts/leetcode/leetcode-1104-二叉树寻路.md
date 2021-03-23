---
title: leetcode-1104. 二叉树寻路
date: 2021-02-24 16:26:01
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [1104. 二叉树寻路](https://leetcode-cn.com/problems/path-in-zigzag-labelled-binary-tree/)

```java
class Solution {
    List<Integer> ans = new ArrayList<>();
    public List<Integer> pathInZigZagTree(int label) {
        // 层数
        int n = 1;
        int x = label;
        while (x / 2 != 0) {
            x = x / 2;
            n++;
        }

        int tmp = label;
        while (n > 0) {
            ans.add(tmp);
            int max = (int)Math.pow(2, n) - 1;
            int min = (int)Math.pow(2, n - 1);
            if (n % 2 == 0) {
                // 偶数层,反向
                while (max != tmp) {
                    max--;
                    min++;
                }

                // 当max==tmp时,min为角标
                tmp = min / 2;
            }else {
                // 奇数层,正向
                while (min != tmp) {
                    max--;
                    min++;
                }
                // min==tmp时,max为数字
                tmp = max / 2;
            }
            n--;
        }
        Collections.sort(ans);
        return ans;
    }
}

```

