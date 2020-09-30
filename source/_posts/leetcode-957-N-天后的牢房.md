---
title: leetcode-957. N 天后的牢房
date: 2020-09-30 15:16:20
categories: 
		- leetcode
tags: 
	- leetcode
cover: https://i.loli.net/2020/09/01/oT6hnNGz4idbgCU.jpg
typora-root-url: ..
---

#### [leetcode-957. N 天后的牢房](https://leetcode-cn.com/problems/prison-cells-after-n-days/)

**原始思路**

```java
class Solution {
    int count = 0;
    Map<String, Integer> map = new HashMap<>();
    public int[] prisonAfterNDays(int[] cells, int N) {
        int[] ans = new int[cells.length];

        dfs(cells, N, ans);
        int k = N % map.size();
        if (k == 0) {
            k = map.size();
        }


        String[] spl = new String[cells.length];
        for (Map.Entry<String, Integer> entry: map.entrySet()) {
            if (entry.getValue() == k) {
                String str = entry.getKey();
                str = str.substring(1, str.length()-1).replace(" ","");
                spl = str.split(",");
                break;
            }
        }

        for (int i = 0; i < cells.length; i++) {
            ans[i] = Integer.parseInt(spl[i]);
        }
        return ans;
    }

    public void dfs(int[] cells, int N, int[] ans){
        if (count >= N) {
            return;
        }

        count++;
        ans[0] = 0;
        ans[ans.length - 1] = 0;
        for (int j = 1; j <= cells.length - 2; j++) {
            if (cells[j - 1] == cells[j + 1]) {
                ans[j] = 1;
            }else {
                ans[j] = 0;
            }
        }

        if (map.containsKey(Arrays.toString(ans))) {
            return;
        }

        map.put(Arrays.toString(ans), count);
        dfs(ans, N, cells);
    }
}
```

