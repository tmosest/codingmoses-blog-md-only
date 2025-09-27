---
title: LeetCode 2176. Count Equal and Divisible Pairs in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **LeetCode 2176 – Count Equal and Divisible Pairs in an Array**

> **Task**  
>  For every pair of indices `(i , j)` (`i < j`) in `nums` count the pair when  
>  1. `nums[i] == nums[j]`  
>  2. `i * j` is divisible by `k`.

The only pairs we ever need to look at are the ones whose values are the same.  
If two indices point to different numbers the pair can never be counted, so we can skip those comparisons.

---

## High‑level idea

1. **Group indices by value** –  
   Scan the array once, storing for each value the indices at which it appears so far.
2. **Check only pairs inside each group** –  
   When we encounter index `i`, we look back at the list of earlier indices that contain the same value.  
   For each earlier index `j` in that list, we test the divisibility condition `i * j % k == 0`.  
   If it holds we increment the answer.
3. **Add the current index** –  
   After checking, we push `i` onto the list for its value so that future indices can pair with it.

This is essentially the “hash‑map‑with‑position‑lists” trick; it turns the naive `O(n²)` brute force into a single‑pass construction that still checks every valid pair, but it never touches pairs of different values.

---

## Correctness proof  

We prove that the algorithm returns exactly the number of valid pairs.

*Let* `pos[v]` be the list of indices we have seen so far whose element is `v`.

When the outer loop is at index `i`:

* For each `j` in `pos[nums[i]]` we check the pair `(j, i)`.  
  Because `j < i` (we only insert indices after we have processed them),  
  this pair satisfies condition 2.  
  The algorithm counts it iff `nums[j] == nums[i]` (true by construction) and  
  `i * j % k == 0`, i.e. iff it satisfies all three conditions.

* All other pairs that could be valid are of the form `(j, i)` where `j < i`
  and `nums[j] == nums[i]`.  
  These are exactly the pairs enumerated in the loop over `pos[nums[i]]`.  
  No pair with `j < i` and `nums[j] != nums[i]` is ever considered,
  because it would never be inserted into `pos[nums[i]]`.

Hence every counted pair satisfies the statement and every pair that satisfies the statement is counted exactly once (the first time its larger index is processed).

∎

---

## Complexity analysis

| Technique | Time | Space | Remarks |
|-----------|------|-------|---------|
| Brute‑force nested loops | \(O(n^2)\) | \(O(1)\) | Unnecessary checks when many values differ. |
| Map‑based one‑pass | \(O(n^2)\) worst‑case (all values equal) | \(O(n)\) | In practice far faster because only same‑value indices are inspected. |

`n ≤ 2·10⁵` on LeetCode, so the map‑based approach passes comfortably.

---

## Reference implementation (Python 3)

```python
from collections import defaultdict
from typing import List

class Solution:
    def countPairs(self, nums: List[int], k: int) -> int:
        # map from value to list of indices seen so far
        pos = defaultdict(list)
        ans = 0
        for i, val in enumerate(nums):
            # look back at earlier indices with the same value
            for j in pos[val]:
                if (i * j) % k == 0:
                    ans += 1
            # record current index for future pairs
            pos[val].append(i)
        return ans
```

The code follows exactly the algorithm described above and runs in the required limits.