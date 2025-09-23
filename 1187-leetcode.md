---
title: LeetCode 1187. Make Array Strictly Increasing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Make Array Strictly Increasing – A Complete Solution Guide  

**Problem Statement**  
You are given two integer arrays `arr1` and `arr2`.  
In one operation you may choose an index `i` in `arr1` and an index `j` in `arr2` and replace `arr1[i]` with `arr2[j]`.  
`arr2` is not modified – you can pick the same element from `arr2` many times.  
The goal is to make `arr1` strictly increasing (`arr1[i] < arr1[i+1]` for all valid `i`) using the minimum number of operations.  
Return that minimum number, or `-1` if it is impossible.

---

## 1.  Key Observations  

1. **We can stop early** – if the first few elements of `arr1` are already strictly increasing, we might not need to touch them.  
2. **Bound on the number of swaps** – each operation changes one element of `arr1`.  
   We never need more than `len(arr1)` operations (each element could be replaced once).  
   Moreover, if we replace more than `len(arr2)` elements, the “extra” swaps are useless because the only thing that matters is the *value* we put in, not the index in `arr2`.  
   Thus the answer is bounded by `min(len(arr1), len(arr2))`.  
3. **Choosing the right value from `arr2`** –  
   If we decide to replace `arr1[i]`, we would like to put the *smallest* value from `arr2` that is still larger than the last value of the increasing prefix.  
   Since `arr2` does not change, we can sort it once and use binary search (`higher()`) to find that value quickly.

---

## 2.  Dynamic‑Programming formulation  

We use the following DP table (or a one‑dimensional version of it):

```
dp[k]  = the smallest possible value of the last element
         of arr1[0 … cur] after we processed the first cur+1 elements
         and used exactly k swaps in total
```

`dp[k]` is `INF` if it is impossible to obtain an increasing prefix with exactly `k` swaps.

**Recurrence** (while iterating over `arr1`):

*Let `prev = dp[k]` be the value of the last element of the prefix before the current position.*

1. **Do not swap current element**  
   If `arr1[i] > prev`, we can keep `arr1[i]`.  
   Then the last value of the new prefix is `arr1[i]` and the number of swaps stays `k`:

   ```
   new_dp[k] = min( new_dp[k] , arr1[i] )
   ```

2. **Swap current element** (only if `k > 0`)  
   We need the smallest element from `arr2` that is larger than the last value of the previous prefix, i.e. larger than `dp[k-1]` (the last value *before* we use the `k`‑th swap).  
   Use binary search on the sorted `arr2` to obtain that value `x`.  
   If such an `x` exists:

   ```
   new_dp[k] = min( new_dp[k] , x )
   ```

Because we always keep the *smallest* possible last value, later positions have the best chance to stay increasing.

---

### 2.1  Initialization  

When we have processed only the first element `arr1[0]`:

```
dp[0]          = arr1[0]                           (no swap)
dp[k] (k>0)    = min( arr1[0] , smallest element in arr2 )
```

The second line says: if we already swapped at least once before the first element,
we can either keep the original value or put the smallest possible value from `arr2`.

---

## 3.  Algorithm (one‑dimensional DP)

```text
sort arr2
p = min(len(arr1), len(arr2))
INF = a number larger than all possible values (e.g. 10**18)

dp = [INF] * (p+1)
dp[0] = arr1[0]
for k in 1..p:                     # use at least one swap on the first element
    dp[k] = min(arr1[0], arr2[0])  # arr2 is sorted, arr2[0] is the smallest element

for i = 1 .. len(arr1)-1:          # process the rest of arr1
    new_dp = [INF] * (p+1)
    for k = 0 .. min(i+1, p):
        # 1) keep arr1[i] if it keeps the strict order
        if arr1[i] > dp[k]:
            new_dp[k] = arr1[i]

        # 2) replace arr1[i] with the smallest arr2 element > dp[k-1]
        if k > 0 and dp[k-1] != INF:
            # binary search in arr2 for the first element strictly greater than dp[k-1]
            x = smallest element in arr2 that is > dp[k-1]
            if x exists:
                new_dp[k] = min(new_dp[k], x)

    dp = new_dp
```

After the loop the answer is the smallest `k` such that `dp[k] != INF`.  
If no such `k` exists, return `-1`.

---

## 4.  Complexity Analysis  

Let  

* `m = len(arr1)`
* `n = len(arr2)`
* `p = min(m, n)` – the maximum answer we need to consider

| Step | Cost |
|------|------|
| Sorting `arr2` | `O(n log n)` |
| DP loop | `O(m * p * log n)` – for each of the `m` positions we iterate up to `p` swap counts, and each swap needs a binary search (`log n`). |
| Memory | `O(p)` – one‑dimensional DP array. |

With `m` and `n` up to `5·10⁴`, this runs comfortably within limits.

---

## 5.  Reference Implementation (Python 3)

```python
import bisect
from typing import List

INF = 10**18

class Solution:
    def makeArrayIncreasing(self, arr1: List[int], arr2: List[int]) -> int:
        arr2.sort()                         # we only need a sorted list
        m, n = len(arr1), len(arr2)
        p = min(m, n)                       # upper bound on swaps needed

        # dp[k] = minimal last value of the prefix after using exactly k swaps
        dp = [INF] * (p + 1)
        dp[0] = arr1[0]                     # no swap on the first element

        # we can also use a swap on the first element (any arr2 value)
        if n > 0:
            dp[1] = min(arr1[0], arr2[0])

        for i in range(1, m):
            new_dp = [INF] * (p + 1)
            for k in range(0, min(i + 1, p) + 1):
                # 1. keep arr1[i]
                if arr1[i] > dp[k]:
                    new_dp[k] = arr1[i]

                # 2. replace arr1[i] if we have at least one swap left
                if k > 0 and dp[k - 1] != INF:
                    # find smallest arr2 element > dp[k-1]
                    idx = bisect.bisect_right(arr2, dp[k - 1])
                    if idx < n:
                        new_dp[k] = min(new_dp[k], arr2[idx])

            dp = new_dp

        # answer is the smallest k with a feasible value
        for k in range(p + 1):
            if dp[k] != INF:
                return k
        return -1
```

---

## 6.  Alternative: Java Implementation (Space‑Optimised)

```java
import java.util.*;

public class Solution {
    public int makeArrayIncreasing(int[] arr1, int[] arr2) {
        int m = arr1.length, n = arr2.length;
        int p = Math.min(m, n);
        final long INF = Long.MAX_VALUE / 4;

        Arrays.sort(arr2);

        long[] dp = new long[p + 1];
        Arrays.fill(dp, INF);
        dp[0] = arr1[0];
        if (n > 0) dp[1] = Math.min(arr1[0], arr2[0]);

        for (int i = 1; i < m; i++) {
            long[] newDp = new long[p + 1];
            Arrays.fill(newDp, INF);
            for (int k = 0; k <= Math.min(i + 1, p); k++) {
                // 1. keep arr1[i]
                if (arr1[i] > dp[k]) newDp[k] = arr1[i];

                // 2. replace arr1[i]
                if (k > 0 && dp[k - 1] != INF) {
                    int idx = upperBound(arr2, dp[k - 1]); // first > dp[k-1]
                    if (idx < n) newDp[k] = Math.min(newDp[k], arr2[idx]);
                }
            }
            dp = newDp;
        }

        for (int k = 0; k <= p; k++) if (dp[k] != INF) return k;
        return -1;
    }

    // binary search for first element > target
    private int upperBound(int[] a, long target) {
        int l = 0, r = a.length;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (a[m] <= target) l = m + 1;
            else r = m;
        }
        return l;
    }
}
```

---

## 7.  Quick “What‑if” check  

| Scenario | Minimum swaps? |
|----------|----------------|
| `arr1 = [1,2,3]` (already increasing) | `0` |
| `arr1 = [3,1,2]`, `arr2 = [4,5]` | `2` (swap 3→4, 1→5) |
| `arr1 = [5,4,3]`, `arr2 = [1,2]` | `-1` (cannot make it increasing) |

---

### Summary  

* Sort `arr2` once.  
* Use DP where `dp[k]` stores the minimal possible last value after `k` swaps.  
* Transition: keep the current element if it keeps the order, or replace it with the smallest `arr2` element that is still larger than the previous prefix value.  
* The answer is the smallest `k` for which a solution exists.  

The algorithm runs in `O(m · min(m,n) · log n)` time and `O(min(m,n))` memory, fully fitting the limits of the problem.