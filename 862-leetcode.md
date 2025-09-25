---
title: LeetCode 862. Shortest Subarray with Sum at Least K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem**

> Given an array `A` ( `1 ≤ |A| ≤ 10^5` , `-10^5 ≤ A[i] ≤ 10^5` ) and an integer `k`  
>  (`1 ≤ k ≤ 10^5`).  
>  Find the length of the *shortest* contiguous sub‑array whose sum is **≥ k**.  
>  If no such sub‑array exists output `-1`.

The naive O(n²) scan is far too slow.  
The optimal solution uses **prefix sums + a monotonic deque** and runs in O(n) time.



--------------------------------------------------------------------

### Key observations

| Observation | Why it matters |
|-------------|----------------|
| **Prefix sums** `P[0] = 0; P[i] = A[0] + … + A[i-1]` | For any sub‑array `[l, r]` (inclusive) the sum equals `P[r+1] – P[l]`.  |
| **Shortest sub‑array condition** | For a fixed end `r` the earliest start that gives a sum ≥ k is the *smallest* index `l` with `P[r+1] – P[l] ≥ k`.  |
| **Monotonic deque of indices** | Keep indices of prefix array in increasing order of prefix value.  
This lets us check the *shortest* valid start in O(1) and guarantees we never need to look at larger prefix values. |

--------------------------------------------------------------------

### Algorithm

```
Let n = |A|
Compute prefix array P[0…n]   // long to avoid overflow
minLen = INF

deque<int> dq        // will store indices of P, increasing order of P[index]

for i from 0 to n:           // i is the index of current prefix value P[i]
    // 1.  Remove all indices from the front that satisfy the requirement
    while dq not empty and P[i] - P[dq.front] >= k:
          minLen = min(minLen, i - dq.front)
          dq.pop_front()

    // 2.  Maintain monotonicity: remove worse prefixes from the back
    while dq not empty and P[i] <= P[dq.back]:
          dq.pop_back()

    // 3.  Append current index
    dq.push_back(i)

if minLen == INF  →  answer = -1
else              →  answer = minLen
```

--------------------------------------------------------------------

### Correctness proof (sketch)

1. **Why step 1 finds all valid sub‑arrays ending at `i`**  
   The deque front always holds the smallest index `l` among those we have seen so far.  
   If `P[i] – P[l] ≥ k` then the sub‑array `(l … i-1)` is valid.  
   Because `l` is the *smallest* such index (by construction of the deque),  
   the sub‑array length `i – l` is the *shortest* ending at `i`.  
   After recording it we remove `l` from the front because any longer sub‑array
   starting at `l` would only be longer.

2. **Why step 2 preserves correctness**  
   If the current prefix `P[i]` is **not larger** than a previous one `P[j]`
   (`j` is at the back), then starting at `j` can never give a shorter sub‑array
   than starting at `i`.  
   Hence `j` is useless for future comparisons and can be discarded.

3. **Why each index enters/exits the deque at most once**  
   - Inserting: we visit each `i` once.  
   - Removing from the front: only when it yields a valid sub‑array, then it
     will never be considered again.  
   - Removing from the back: when a later prefix is smaller, the older one
     can never be useful again because all future differences will be
     **≥** the newer, smaller prefix.  

   Thus overall O(n) operations.

4. **Why we output `-1` when `minLen` is still INF**  
   It means no pair of indices satisfied `P[i] – P[l] ≥ k` during the whole scan,
   i.e. no sub‑array has sum ≥ k.

--------------------------------------------------------------------

### Complexity analysis

| Operation | Time | Space |
|-----------|------|-------|
| Prefix sums | **O(n)** | **O(n)** |
| Deque processing | **O(n)** | **O(n)** |
| Total | **O(n)** | **O(n)** |

Both bounds satisfy the required limits.

--------------------------------------------------------------------

### Reference implementation (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int shortestSubarray(vector<int>& A, int k) {
        int n = A.size();
        vector<long long> pref(n + 1, 0);
        for (int i = 0; i < n; ++i)
            pref[i + 1] = pref[i] + A[i];

        deque<int> dq;              // indices of pref[]
        int minLen = INT_MAX;

        for (int i = 0; i <= n; ++i) {
            // 1. Check valid sub‑arrays ending at i
            while (!dq.empty() && pref[i] - pref[dq.front()] >= k) {
                minLen = min(minLen, i - dq.front());
                dq.pop_front();
            }

            // 2. Maintain monotonicity
            while (!dq.empty() && pref[i] <= pref[dq.back()]) {
                dq.pop_back();
            }

            // 3. Append current index
            dq.push_back(i);
        }

        return (minLen == INT_MAX) ? -1 : minLen;
    }
};
```

The same logic can be ported to Java, Python, JavaScript, Go, etc.  
All follow the identical `for` loop with two while‑loops and a deque.

--------------------------------------------------------------------

**Result**

Using the prefix‑sum + monotonic‑deque approach gives an **O(n)** solution that
passes all test cases within the strict time limits.  
If you have any more questions or need the code in a different language, let me know!