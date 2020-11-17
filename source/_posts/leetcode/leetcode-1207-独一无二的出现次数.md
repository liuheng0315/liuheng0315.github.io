---
title: leetcode-1207. 独一无二的出现次数
date: 2020-10-28 09:05:48
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1207. 独一无二的出现次数](https://leetcode-cn.com/problems/unique-number-of-occurrences/)

```java
class Solution {
    public boolean uniqueOccurrences(int[] arr) {
        Arrays.sort(arr);
        int n = arr.length;
        boolean[] st = new boolean[n];
        int count = 1;
        for(int i = 1; i < n; i++){
            if(arr[i] == arr[i-1]){
                count++;
            }else{
                if(st[count] == true){
                    return false;
                }
                st[count] = true;
                count = 1;
            }

            if(i== n-1 && st[count] == true){
                    return false;
            }
        }
        return true;
    }
}
```

