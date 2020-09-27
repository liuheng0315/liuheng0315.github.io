---
title: leetcode-1544. 整理字符串
date: 2020-09-25 14:06:31
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1544. 整理字符串](https://leetcode-cn.com/problems/make-the-string-great/)

```java
class Solution {
    public String makeGood(String s) {
        int n = s.length();
        ArrayList<Character> list = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            list.add(s.charAt(i));
        }

        while (judge(list)) {
            for (int i = 1; i < list.size(); i++) {
                Character pre = list.get(i - 1);
                Character cur =list.get(i);
                if ((Character.toUpperCase(pre) == cur
                        || pre == Character.toUpperCase(cur)) && pre != cur){
                    list.remove(i-1);
                    list.remove(i-1);
                    i--;
                }
            }
        }

        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < list.size(); i++) {
            sb.append(list.get(i));
        }

        return sb.toString();
    }

    public boolean judge(List<Character> list){
        for (int i = 1; i < list.size(); i++) {
            Character pre = list.get(i - 1);
            Character cur =list.get(i);
            if ((Character.toUpperCase(pre) == cur
                    || pre == Character.toUpperCase(cur)) && pre != cur){
                return true;
            }
        }
        return false;
    }
}
```

