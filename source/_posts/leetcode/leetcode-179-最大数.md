---
title: leetcode-179. 最大数
date: 2021-04-12 09:32:12
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [179. 最大数](https://leetcode-cn.com/problems/largest-number/)

```java
class Solution {
    public String largestNumber(int[] nums) {
        Queue<Integer> queue = new PriorityQueue<>((o1,o2)->{
            String num1 = "" + o1 + o2;
            String num2 = "" + o2 + o1;
            return num2.compareTo(num1);
        });

        for(int i = 0; i < nums.length; i++){
            queue.offer(nums[i]);
        }

        StringBuffer buffer = new StringBuffer();
        while(!queue.isEmpty()){
            buffer.append(queue.poll());
        }
        String ans = buffer.toString();
        if(ans.startsWith("0")){
            return "0";
        }

        return ans;
    }
}
```

