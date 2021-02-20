---
title: leetcode-697. 数组的度
date: 2021-02-20 11:01:13
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [697. 数组的度](https://leetcode-cn.com/problems/degree-of-an-array/)

```java
class Solution {
    public int findShortestSubArray(int[] nums) {
        // 寻找最大的度
        Map<Integer, Integer> map = new HashMap<>();
        int max = 0;
        for(int num : nums){
            map.put(num, map.getOrDefault(num, 0) + 1);
            max = Math.max(max, map.get(num));
        }

        // 找出最大度的数字
        List<Integer> list = new ArrayList<>();
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (entry.getValue() == max) {
                list.add(entry.getKey());
            }
        }

        // 寻找最小的长度
        int ans = Integer.MAX_VALUE;
        for (Integer num : list) {
            int i = 0;
            int j = nums.length -1;

            while (nums[i] != num) {
                i++;
            }

            while (nums[j] != num) {
                j--;
            }
            ans = Math.min(ans, j - i + 1);
        }

        return ans;
    }
}
```

