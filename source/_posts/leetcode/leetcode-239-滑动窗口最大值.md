---
title: leetcode-239. 滑动窗口最大值
date: 2021-09-01 18:27:30
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ../..
---

#### [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

优先队列

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] ans = new int[nums.length - k + 1];
        // 使用优先队列
        Queue<int[]> queue = new PriorityQueue<int[]>((o1, o2)->{
            if(o2[0] - o1[0] > 0){
                return 1;
            }else if(o2[0] - o1[0] < 0){
                return -1;
            }else{
                if(o2[1] - o1[1] >= 0){
                    return 1;
                }else{
                    return -1;
                }
            } 
        });

        // 将前k个数据先入队列
        for(int i = 0; i < k; i++){
            queue.offer(new int[]{nums[i], i});
        }

        ans[0] = queue.peek()[0];

        // 遍历剩下的数
        for(int i = k; i < nums.length; i++){
            while(!queue.isEmpty() && queue.peek()[1] < (i - k + 1)){
                queue.poll();
            }
            queue.offer(new int[]{nums[i], i});
            ans[i-k+1] = queue.peek()[0];
        }
        return ans;
    }
}
```



双端队列

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] ans = new int[n-k+1];

        Deque<Integer> queue = new LinkedList<>();
        for(int i = 0; i < k; i++){
            while(!queue.isEmpty() && nums[i] > nums[queue.peekLast()]){
                queue.pollLast();
            }
            queue.offerLast(i);
        }

        ans[0] = nums[queue.peekFirst()];

        for(int i = k; i < n; i++){
            while((!queue.isEmpty()) && nums[i] > nums[queue.peekLast()]){
                queue.pollLast();
            }

            while((!queue.isEmpty()) && queue.peekFirst() < i - k + 1){
                queue.pollFirst();
            }

            queue.offerLast(i);
            ans[i-k+1] = nums[queue.peekFirst()];
        }
        return ans;
    }
}
```

