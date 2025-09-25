---
title: LeetCode 3240. Minimum Number of Flips to Make Binary Grid Palindromic II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Answer**

For an even‑sized binary matrix the problem splits into independent 4‑cell groups.  
Let

```
(i , j)            – top‑left corner of the group
(i , m-1-j)        – top‑right corner
(n-1-i , j)        – bottom‑left corner
(n-1-i , m-1-j)    – bottom‑right corner
```

(Here `n` is the number of rows and `m` the number of columns.)

For each group we can either make all four cells 0 or all four cells 1 –  
both choices guarantee that every 2×2 submatrix has an even number of 1’s.  
The cost of the two choices is

```
cnt          = sum of the four cells
costAllZero  = cnt          // flip all 1→0
costAllOne   = 4 - cnt      // flip all 0→1
```

Hence the minimal cost for that group is `min(cnt, 4‑cnt)`.  
Adding the costs over all groups gives the global optimum because the
groups are disjoint and independent.

```java
class Solution {
    public int minFlips(int[][] grid) {
        int n = grid.length, m = grid[0].length;
        int ans = 0;
        for (int i = 0; i < n / 2; ++i) {
            for (int j = 0; j < m / 2; ++j) {
                int cnt = grid[i][j] + grid[i][m-1-j]
                        + grid[n-1-i][j] + grid[n-1-i][m-1-j];
                ans += Math.min(cnt, 4 - cnt);
            }
        }
        return ans;
    }
}
```

So, **the minimum number of flips equals the sum of `min(cnt, 4‑cnt)` over all the
4‑cell groups**.  
This solution runs in **O(n · m)** time and uses **O(1)** extra space.