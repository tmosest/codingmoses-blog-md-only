---
title: LeetCode 3555. Smallest Subarray to Sort in Every Sliding Window - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Python – Straight‑forward “sort‑and‑compare” solution**  
*Time complexity:* `O((n−k+1) · k log k)`  
*Space complexity:* `O(k)`

The idea is simple:

1. For every sliding window of length `k` take the sub‑array `sub`.
2. Sort a copy of that sub‑array (`sorted_sub`).
3. Scan the two arrays simultaneously.  
   *If an element of `sub` is different from the element at the same index in
   `sorted_sub`, that position is “out of place”.*  
   The smallest contiguous block that contains all out‑of‑place positions
   is exactly the block that must be sorted to make the whole window sorted.

The length of that block is `right – left + 1`.  
If the window is already sorted, `left` never moves from `∞` and we return `0`.

```python
from typing import List

class Solution:
    def minSubarraySort(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        ans = []

        # slide over all windows of length k
        for i in range(n - k + 1):
            sub = nums[i:i + k]          # current window
            sorted_sub = sorted(sub)     # its sorted version

            left, right = k, -1          # will store min and max mismatched indices

            # find the leftmost and rightmost mismatched positions
            for j in range(k):
                if sub[j] != sorted_sub[j]:
                    left = min(left, j)
                    right = max(right, j)

            # if no mismatches were found, the window is already sorted
            if right == -1:              # no mismatches
                ans.append(0)
            else:
                ans.append(right - left + 1)

        return ans
```

### How it works
* For each window we have the “current” order (`sub`) and the “desired” order
  (`sorted_sub`).
* Every index where they differ is a place that is not in the correct
  relative position in the window.
* The smallest sub‑array that contains all such indices is precisely the
  segment that has to be sorted.  
  Its length is `right – left + 1`.  
  If the window is already sorted, no indices differ, and we return `0`.

The solution is easy to understand, implements the core idea directly,
and is fast enough for the typical LeetCode constraints.