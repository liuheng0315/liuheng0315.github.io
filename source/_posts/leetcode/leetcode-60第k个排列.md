---
title: leetcode-60.第k个排列
date: 2020-09-05 15:48:33
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

[leetcode-60.第k个排列](https://leetcode-cn.com/problems/permutation-sequence/)

**思路解析**

```java
public class Solution {
    public String getPermutation(int n, int k) {
        Set<Integer> set = new HashSet<>();
        AtomicInteger atomic = new AtomicInteger(k);
        StringBuffer sb = new StringBuffer();
        for (int i = 1; i <= n; i++) {
            sb.append(getIndexNum(n, atomic, i, set));
        }
        return sb.toString();
    }

    //确认每一位数字
    public static Integer getIndexNum(int n, AtomicInteger atomic, int index, Set<Integer> set) {
        int result = 0;

        if (index == n) {
            //说明前n-1位已经确定，那么最后一位也可以确定
            for (int i = 1; i <= n; i++) {
                if (!set.contains(i)) {
                    return i;
                }
            }
        }

        //每个index上该数字出现最多的次数
        int fb = fb(n - index);

        //取除数,i=1表示在第一个数字上，i=1表示在第二个数字上....依次类推
        int i = (atomic.get() - 1) / fb + 1;
        for (int j = 1; j <= n; j++) {
            if (!set.contains(j)) {
                i--;
                if (i == 0) {
                    //找到了第i个数为index上的数
                    result = j;
                    set.add(result);
                    //更新atomic的值
                    atomic.set(atomic.get() - (((atomic.get() - 1) / fb + 1) - 1) * fb);
                    break;
                }
            }
        }
        //返回此时确认的数字
        return result;
    }

    //斐波拉切函数
    public static Integer fb(int k) {
        if (k == 1) {
            return 1;
        }
        return k * fb(k - 1);
    }

}
```