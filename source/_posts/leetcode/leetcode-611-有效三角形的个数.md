---
title: leetcode-611. 有效三角形的个数
date: 2021-08-05 10:53:53
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [611. 有效三角形的个数](https://leetcode-cn.com/problems/valid-triangle-number/)

```java
class Solution {
    public int triangleNumber(int[] nums) {
        int ans = 0;
        // 先排序
        Arrays.sort(nums);
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            for (int j = i+1; j < n; j++) {
                // 只需要a+b>c即可
                int a = nums[i];
                int b = nums[j];
                // 二分查找j+1~n-1的范围内符合条件的数据,使用二分查找
                int l = j+1;
                int r = n-1;
                int k = j;
                while (l <= r){
                    int mid = (l+r)/2;
                    if ((a+b) > nums[mid]){
                        l = mid + 1;
                        k = mid;
                    }else{
                        r = mid - 1;
                    }
                }
                ans += k - j;
            }
        }
        return ans;
    }
}
```

