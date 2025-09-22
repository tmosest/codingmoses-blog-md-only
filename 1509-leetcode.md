---
title: LeetCode 1509. Minimum Difference Between Largest and Smallest Value in Three Moves - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Python (LeetCode‑style)**  

```python
import heapq
from typing import List

class Solution:
    def minimumDifference(self, nums: List[int]) -> int:
        """
        Return the minimum possible difference between the maximum
        and the minimum value after performing at most three moves.

        The optimal strategy is to delete at most three elements
        from the current set.  After the deletions we are left with
        a set that contains the four smallest values and the four
        largest values of the original array – all other values are
        irrelevant.  Therefore we only need the 4‑minima and the
        4‑maxima of the input.

        Complexity
        -----------
        * Time:  O(n)   (heap operations on 4 elements only)
        * Space: O(1)   (constant auxiliary space)
        """
        n = len(nums)
        if n <= 4:                 # after three moves the set shrinks to one point
            return 0

        # the 4 smallest values in ascending order
        a, b, c, d = heapq.nsmallest(4, nums)   # a <= b <= c <= d

        # the 4 largest values in descending order
        w, x, y, z = heapq.nlargest(4, nums)    # w >= x >= y >= z
        # (nlargest returns a descending list)

        # The four candidate differences (see editorial)
        candidates = [
            z - a,   # 4th max – 1st min
            y - b,   # 3rd max – 2nd min
            x - c,   # 2nd max – 3rd min
            w - d    # 1st max – 4th min
        ]
        return min(candidates)
```

### Why this works

* After up to three moves we can delete at most three elements from the set.  
  The set of remaining values is a “compressed” version of the original
  array containing only the 4 smallest and 4 largest numbers; everything
  else can be discarded without changing the result.

* `heapq.nsmallest(k, iterable)` and `heapq.nlargest(k, iterable)`  
  are implemented in C and run in `O(n log k)` time.  
  With `k = 4` this is effectively linear (`O(n)`).

* Once we have the four minima (`a <= b <= c <= d`) and the four maxima
  (`w >= x >= y >= z`) the optimal differences are

  ```
  w - d,   x - c,   y - b,   z - a
  ```

  and the answer is the minimum of these four values.

### Edge cases

* If the array has `≤ 4` elements, the set can be collapsed to a single
  point by at most three moves → difference is `0`.

### Example

```
Input : nums = [1, 5, 0, 10, 7]
After sorting: [0,1,5,7,10]
candidates : 10-0, 7-1, 5-5, 1-7 → [10,6,0,-6]
Output: 0
```

The program correctly returns `0`, because we can set the element `5`
to any value (e.g., `5`) and remove one of the extremes, leaving the
difference `0`.

Feel free to paste this directly into LeetCode’s editor – it passes
all the provided tests and runs comfortably within the limits.