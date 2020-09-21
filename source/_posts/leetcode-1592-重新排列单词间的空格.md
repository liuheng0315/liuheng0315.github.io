---
title: leetcode-1592. 重新排列单词间的空格
date: 2020-09-21 11:06:02
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-1592. 重新排列单词间的空格](https://leetcode-cn.com/problems/rearrange-spaces-between-words/)

```java
class Solution {
    public String reorderSpaces(String text) {
        // 记录单词和空格
        List<String> list = new ArrayList<>();
        int num = 0;
        StringBuffer buffer = new StringBuffer();
        for (int i = 0; i < text.length(); i++) {
            if (text.charAt(i) == ' ') {
                num++;
                if (buffer.length() > 0) {
                    list.add(buffer.toString());
                    buffer.delete(0, buffer.length());
                }
            }else {
                buffer.append(text.charAt(i));
            }
        }

        if (buffer.length() > 0) {
            //将剩余的添加到数组
            list.add(buffer.toString());
        }
        //清空buffer
        buffer.delete(0, buffer.length());

        if (num == 0) {
            return text;
        }

        int k = list.size() - 1;
        if (k == 0) {
            //说明没有间隔
            buffer.append(list.get(0));
            for (int i = 0; i < num; i++) {
                buffer.append(' ');
            }
            return buffer.toString();
        }


        //计算每个单词间可以放多少个
        int j = num / k;
        //余数
        int r = num % k;

        for (int i = 0; i < list.size(); i++) {
            buffer.append(list.get(i));
            if (i == list.size() - 1) {
                continue;
            }
            for (int l = 0; l < j; l++) {
                buffer.append(' ');
            }
        }

        for (int i = 0; i < r; i++) {
            buffer.append(' ');
        }
        return buffer.toString();
    }
}
```

