---
title: leetcode-332.重新安排行程
categories: 
		- leetcode
tags: 
	- leetcode
cover: /images/leetcode.jpg
typora-root-url: ..
---

[leetcode-332.重新安排行程](https://leetcode-cn.com/problems/reconstruct-itinerary/)

**原始思路** ***---------------------------没做过--超时了,看完图再做！！！！！！！！！***

```java
class Solution {
    List<List<String>> result = new ArrayList<>();
    List<String> tmp = new ArrayList<>();
    //记录起始位置
    List<String> strs = new ArrayList<>();
    Set<String> set = new HashSet<>();

    public List<String> findItinerary(List<List<String>> tickets) {
        strs.add("JFK");
        dfs(0, tickets, null);

        Collections.sort(result, new Comparator<List<String>>() {
            @Override
            public int compare(List<String> o1, List<String> o2) {
                for (int i = 0; i < o1.size(); i++) {
                    if (!o1.get(i).equals(o2.get(i))){
                        return o1.get(i).compareTo(o2.get(i));
                    }
                }
                return 0;
            }
        });

        return result.get(0);
    }

    public void dfs(int begin, List<List<String>> tickets, List<String> preTicket){

        if (begin >= tickets.size()){
            return;
        }

        if (strs.size() == tickets.size() + 1){
            result.add(new ArrayList<String>(strs));
            return;
        }

        for (int i = begin; i < tickets.size(); i++){
            //如果后面的全部正确也不满足,则return
            if(begin > strs.size()){
                return;
            }

            if (strs.get(strs.size() - 1).equals(tickets.get(i).get(0))){
                //已经用过的ticket不能再用
                if (set.contains(tickets.get(i).toString() + i)){
                    continue;
                }else {
                    set.add(tickets.get(i).toString() + i);
                }
                tmp.add(tickets.get(i).get(0));
                strs.add(tickets.get(i).get(1));
                dfs(begin, tickets, tickets.get(i));
                tmp.remove(tmp.size() -1);
                strs.remove(strs.size() -1);
                set.remove(tickets.get(i).toString() + i);
            }
        }
    }
}
```

