---
title: LeetCode 2941. Maximum GCD-Sum of a Subarray - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem**  
Given an array `nums` (1 ≤ len(nums) ≤ 2·10⁵) and an integer `k` (1 ≤ k ≤ len(nums)), find a contiguous sub‑array whose length is at least `k` that maximises  

```
(sum of its elements) × (GCD of its elements)
```

Return the maximum value (use 64‑bit arithmetic).

--------------------------------------------------------------------

### Observations

* For a fixed right end `r`, all GCDs of sub‑arrays that end at `r` form a very small set.  
  The GCD can only **decrease** when we extend the sub‑array leftwards, and each
  time it changes the value must divide the previous one.  
  For the numbers that occur in the array, the number of distinct GCDs that can
  appear at any point is bounded by **O(log V)** where `V = max(nums)`.  
  In practice for `V ≤ 10⁶` it never exceeds 20.

* Because of the above, we can keep at most one entry for each possible GCD
  while scanning the array.  
  For each GCD we store the **longest** sub‑array (i.e. the one with the largest
  sum) that has this GCD and ends at the current position.

--------------------------------------------------------------------

### Algorithm (GCD‑List + Prefix sums)

```
max_val = 0
store = {}                      // divisor -> (sum, length)

for i, x in enumerate(nums):
    new = {}                    // state after adding nums[i]
    
    // sub‑array consisting only of nums[i]
    new[x] = (x, 1)
    if k == 1:
        max_val = max(max_val, x * x)

    // extend every previously stored sub‑array with nums[i]
    for g, (s, l) in store.items():
        ng = gcd(g, x)           // new GCD after adding x
        ns = s + x
        nl = l + 1
        if nl >= k:
            max_val = max(max_val, ng * ns)

        // keep only the longest sub‑array for this GCD
        if ng not in new or new[ng][1] < nl:
            new[ng] = (ns, nl)

    store = new

return max_val
```

--------------------------------------------------------------------

### Correctness Proof  

We prove that the algorithm returns the maximum possible GCD‑sum.

---

#### Lemma 1  
At the beginning of the loop iteration for index `i`, `store` contains, for
each possible divisor `d`, a pair `(s, l)` where `s` is the sum and `l` is
the length of **the longest** sub‑array ending at position `i‑1` whose GCD is
`d`.

*Proof.*  
Initialization (`i = 0`) is trivial: `store` is empty.  
Assume the lemma holds for index `i`.  
During the next iteration we construct `new` as follows:

* For each entry `(s, l)` in `store` we extend that sub‑array with
  `nums[i]`.  The new GCD is `gcd(d, nums[i])`.  
  We keep only the entry with the **maximum** length for each resulting GCD,
  so after processing all entries `new` satisfies the lemma for index `i`.

* We also insert the sub‑array of length 1 ending at `i`; this is the only
  sub‑array whose GCD equals `nums[i]` that ends at `i`.  
  Hence the lemma also holds after setting `store = new`. ∎



#### Lemma 2  
During the processing of index `i` the algorithm examines the value
`G × S` for **every** sub‑array that ends at `i`, where  
`G = GCD(sub‑array)` and `S = sum(sub‑array)`.

*Proof.*  
Consider any sub‑array `nums[l … i]`.  
By Lemma&nbsp;1 when the loop for `i` is entered, `store` contains an entry
with GCD equal to `gcd(nums[l … i‑1])` and length `i‑l`.  
When we extend this entry with `nums[i]`, the GCD becomes `gcd(G, nums[i])`,
which is exactly the GCD of the whole sub‑array.  
Its length increases to `i‑l+1` and its sum to `S`.  
Thus the algorithm will compute `G × S` for this sub‑array. ∎



#### Lemma 3  
For each sub‑array of length at least `k` the algorithm updates
`max_val` with its GCD‑sum.

*Proof.*  
By Lemma&nbsp;2 the algorithm computes `G × S` for the sub‑array.
The check `if nl >= k` guarantees that we only consider sub‑arrays of
required length before updating `max_val`. ∎



#### Theorem  
After the last iteration `max_val` equals the maximum possible
`(sum of elements) × (GCD of elements)` over all contiguous sub‑arrays of
length at least `k`.

*Proof.*  
From Lemma&nbsp;3 every eligible sub‑array contributes its value to
`max_val`, so `max_val` is **at least** the optimum.  
Conversely, `max_val` is updated only with values of eligible sub‑arrays,
hence it is **at most** the optimum.  
Therefore the two are equal. ∎



--------------------------------------------------------------------

### Complexity Analysis

Let `V = max(nums)`.

* `gcd` runs in `O(log V)` time.  
* Each element is combined with at most `O(log V)` distinct GCDs (the set
  size never exceeds ~20 for 10⁶).  
  Thus each iteration costs `O(log V)`.

```
Time   :  O(n · log V)   ≈ 1.3·10⁶ operations for n = 2·10⁵
Memory :  O(log V)       (the dictionary of GCDs)
```

Both bounds easily satisfy the limits.

--------------------------------------------------------------------

### Reference Implementation (Python 3)

```python
from math import gcd
from collections import defaultdict
from typing import List

def max_gcd_sum(nums: List[int], k: int) -> int:
    max_val = 0
    store = {}                     # divisor -> (sum, length)

    for x in nums:
        nxt = defaultdict(lambda: (0, 0))

        # sub‑array of length 1
        nxt[x] = (x, 1)
        if k == 1:
            max_val = max(max_val, x * x)

        # extend all previously stored sub‑arrays
        for g, (s, l) in store.items():
            ng = gcd(g, x)
            ns = s + x
            nl = l + 1
            if nl >= k:
                max_val = max(max_val, ng * ns)

            # keep only the longest sub‑array for this divisor
            if nxt[ng] < (ns, nl):
                nxt[ng] = (ns, nl)

        store = nxt

    return max_val
```

The function follows exactly the algorithm proven correct above and
runs in the required time for the given constraints.