---
title: leetcode-781. 森林中的兔子
date: 2021-04-06 18:41:52
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [781. 森林中的兔子](https://leetcode-cn.com/problems/rabbits-in-forest/)

```java
class Solution {
    public int numRabbits(int[] answers) {
        int ans = 0;
        if(answers.length == 0){
            return 0;
        }
        Arrays.sort(answers);
        int k = 0;
        for(int i = 0; i < answers.length; i++){
            if(k == 0){
                ans += answers[i] + 1;
                k = answers[i];
                continue;
            }

            if(answers[i] == answers[i-1] && k > 0){
                k--;
                continue;
            }

            if(answers[i] != answers[i-1]){
                ans += answers[i] + 1;
                k = answers[i];
                continue;
            }
        }
        return ans;
    }
}
```

