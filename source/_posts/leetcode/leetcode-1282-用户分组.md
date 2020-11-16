---
title: leetcode-1282. 用户分组
date: 2020-10-15 12:55:19
categories: 
		- leetcode
tags: 
	- leetcode
cover: images/leetcode.jpg
typora-root-url: ..
---

#### [leetcode-1282. 用户分组](https://leetcode-cn.com/problems/group-the-people-given-the-group-size-they-belong-to/)

```java
class Solution {
    public List<List<Integer>> groupThePeople(int[] groupSizes) {
        Map<Integer, List<Integer>> map = new HashMap<>();
        for(int i = 0; i < groupSizes.length; i++){
            if(map.containsKey(groupSizes[i])){
                map.get(groupSizes[i]).add(i);
            }else{
                List<Integer> tmp = new ArrayList<>();
                tmp.add(i);
                map.put(groupSizes[i], tmp);
            }
        }
        List<List<Integer>> result = new ArrayList<>();

        for(Map.Entry<Integer, List<Integer>> entry : map.entrySet()){
            //进行拆分
            int size = entry.getKey();
            List<Integer> list = entry.getValue();
            if(list.size()>size){
                int l = 0;
                int r = size;
                for (int i = 0; i < list.size()/size; i++) {
                    result.add(list.subList(l, r));
                    l = r;
                    r = r + size;
                }
            }else{
                result.add(entry.getValue());
            }
    }
        return result;
    }
}
```

