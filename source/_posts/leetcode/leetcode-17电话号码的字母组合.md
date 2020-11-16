---
title: leetcode-17.电话号码的字母组合
date: 2020-09-10 11:59:31
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

[leetcode-17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

**原始思路**

```java
class Solution {
    List<String> result = new ArrayList<>();
    StringBuffer sb = new StringBuffer();
    public List<java.lang.String> letterCombinations(String digits) {
        //初始化数组,输入如23
        String[] arr = new String[]{"a","b","c","d","e","f","g","h","i","j","k","l","m","n","o","p","q","r","s","t","u","v","w","x","y","z"};
        int[] chr = new int[digits.length()];
        for (int i = 0; i < digits.length(); i++){
            chr[i] = Integer.parseInt(java.lang.String.valueOf(digits.charAt(i)));
        }
        dfs(arr, chr, 0);
        return result;
    }

    public void dfs(java.lang.String[] arr, int[] chr, int chrIndex){
        if (sb.length() == chr.length){
            if (sb.length() > 0){
                result.add(sb.toString());
            }
            return;
        }

        if (chrIndex > chr.length){
            return;
        }
        int begin = 0;
        int end = 0;
        if (chr[chrIndex] == 7){
            begin = (chr[chrIndex]-2)*3;
            end = (chr[chrIndex]-1)*3 ;
        }else if (chr[chrIndex] == 8){
            begin = (chr[chrIndex]-2)*3 + 1;
            end = (chr[chrIndex]-1)*3 ;
        }else if (chr[chrIndex] == 9){
            begin = (chr[chrIndex]-2)*3 + 1;
            end = arr.length -1;
        }else{
            begin = (chr[chrIndex]-2)*3;
            end = (chr[chrIndex]-1)*3 - 1;
        }

        for (int i = begin; i<= end; i++){
            sb.append(arr[i]);
            dfs(arr, chr, chrIndex + 1);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
}
```

