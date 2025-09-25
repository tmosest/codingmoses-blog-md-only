---
title: LeetCode 1840. Maximum Building Height - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Maximum Building Height – LeetCode 1840  
### (Hard | Greedy | O(M log M) –  M = #restrictions)

> **Problem**  
> You are given `n` new buildings in a line (`1 … n`).  
> * Building 1 must have height 0.  
> * Adjacent buildings may differ by at most 1.  
> * Some buildings have an upper limit: `restrictions[i] = [id, maxHeight]`.  
> Return the **maximum possible height** of the tallest building.

> **Constraints**  
> * `2 ≤ n ≤ 10⁹`  
> * `0 ≤ restrictions.length ≤ min(n-1, 10⁵)`  
> * Each `id` is unique and ≠ 1.  
> * `0 ≤ maxHeight ≤ 10⁹`

> **Goal** – write clean, production‑ready code in Java, Python, and C++ and explain the *good*, *bad*, and *ugly* parts of the solution.

---

## 1. Intuition & Greedy Insight

The only thing that limits a building’s height is the *closest restriction* on each side and the *adjacent‑difference* rule.

If we look at two consecutive restrictions (or the virtual restriction at building 1), the heights we can assign to the buildings in between must satisfy

```
|h[j] – h[j+1]| ≤ 1  for all j in that interval
```

and also cannot exceed the two “boundary” heights.  
Given the two boundary heights `hL` (at id `x`) and `hR` (at id `y`), the tallest possible height inside the interval is simply

```
max(hL, hR) + ( (y – x) – |hL – hR| ) / 2
```

because we can climb up from the lower boundary until we run out of distance.  
So the problem reduces to

1. **Order all restrictions by id** (plus the mandatory point `1,0`).
2. **Clip** each restriction’s height from both sides so that the difference to its neighbor never exceeds the distance between them.
3. **Scan each interval** and compute the peak height achievable there.

That is a classic greedy sweep.  

> **Why it works** – The constraints are all *local* (adjacent differences) and *monotonic* (no benefit to raising a building beyond the nearest restriction). By forcing the heights to be as low as necessary on both sides, we guarantee that we can always satisfy all restrictions while keeping the possibility of a high peak untouched.

---

## 2. Edge Cases & “Ugly” Parts

| Bad | Ugly |
|-----|------|
| Large `n` (up to 1 billion) – iterating over every building is impossible. | Need to use 64‑bit integers (`long` / `long long`) because `maxHeight + distance` may overflow 32‑bit. |
| No restrictions at all – the tallest building is simply at position `n`. | Careful handling of the *virtual* restriction at `n` (there is no real upper bound, but the adjacent‑difference rule still applies). |
| Restrictions may not include building 1 or building n. | Must insert them into the list before processing. |
| Sorting O(M log M) where M ≤ 10⁵ is fine, but must be implemented correctly. | Using a pair/tuple that mixes int and long types can lead to subtle bugs. |

---

## 3. The Optimal Algorithm

1. **Add the mandatory point** `(1, 0)` to the list of restrictions.
2. **Sort** the list by building id.
3. **Forward pass** – for each restriction `i` (starting from the second), set  
   `height[i] = min( height[i], height[i-1] + (id[i] - id[i-1]) )`
4. **Backward pass** – for each restriction `i` (starting from the second‑last), set  
   `height[i] = min( height[i], height[i+1] + (id[i+1] - id[i]) )`
5. **Compute the answer**  
   * Initialise `ans = 0`.  
   * For each consecutive pair `(i, i+1)` in the list, let `d = id[i+1] - id[i]`, `hL = height[i]`, `hR = height[i+1]`.  
     `peak = max(hL, hR) + ( d - abs(hL - hR) ) / 2`.  
     `ans = max(ans, peak)`.  
   * Also handle the last segment to building `n`: `peak = height[last] + (n - id[last])`.  
     Update `ans` accordingly.

The algorithm runs in **O(M log M)** time and **O(M)** memory – perfectly suited for the limits.

---

## 4. Code

Below are fully‑commented implementations in **Java**, **Python**, and **C++**.  
All three use 64‑bit integers to avoid overflow.

---

### 4.1 Java

```java
import java.util.*;

/**
 * LeetCode 1840 - Maximum Building Height
 * Greedy O(M log M) solution
 */
class Solution {

    public int maxBuilding(int n, int[][] restrictions) {
        // List of {id, height}
        List<long[]> list = new ArrayList<>();

        // Mandatory starting point
        list.add(new long[]{1L, 0L});

        // Add real restrictions
        for (int[] r : restrictions) {
            list.add(new long[]{r[0], r[1]});
        }

        // Sort by id
        list.sort(Comparator.comparingLong(a -> a[0]));

        int m = list.size();

        // Forward pass
        for (int i = 1; i < m; i++) {
            long dist = list.get(i)[0] - list.get(i - 1)[0];
            long allowed = list.get(i - 1)[1] + dist;
            if (list.get(i)[1] > allowed) {
                list.get(i)[1] = allowed;
            }
        }

        // Backward pass
        for (int i = m - 2; i >= 0; i--) {
            long dist = list.get(i + 1)[0] - list.get(i)[0];
            long allowed = list.get(i + 1)[1] + dist;
            if (list.get(i)[1] > allowed) {
                list.get(i)[1] = allowed;
            }
        }

        // Compute answer
        long ans = 0;

        // Intervals between restrictions
        for (int i = 0; i < m - 1; i++) {
            long idL = list.get(i)[0];
            long hL  = list.get(i)[1];
            long idR = list.get(i + 1)[0];
            long hR  = list.get(i + 1)[1];

            long dist = idR - idL;
            long peak = Math.max(hL, hR) + (dist - Math.abs(hL - hR)) / 2;
            if (peak > ans) ans = peak;
        }

        // Last segment to building n (no upper restriction)
        long lastId = list.get(m - 1)[0];
        long lastH  = list.get(m - 1)[1];
        long peakEnd = lastH + (n - lastId);
        if (peakEnd > ans) ans = peakEnd;

        // ans fits in 32‑bit because constraints guarantee it
        return (int) ans;
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def maxBuilding(self, n: int, restrictions: List[List[int]]) -> int:
        # List of tuples (id, height) as ints
        lst = [(1, 0)]
        lst.extend(restrictions)

        # Sort by id
        lst.sort(key=lambda x: x[0])

        # Forward pass
        for i in range(1, len(lst)):
            dist = lst[i][0] - lst[i-1][0]
            allowed = lst[i-1][1] + dist
            if lst[i][1] > allowed:
                lst[i] = (lst[i][0], allowed)

        # Backward pass
        for i in range(len(lst)-2, -1, -1):
            dist = lst[i+1][0] - lst[i][0]
            allowed = lst[i+1][1] + dist
            if lst[i][1] > allowed:
                lst[i] = (lst[i][0], allowed)

        # Compute answer
        ans = 0
        for i in range(len(lst)-1):
            idL, hL = lst[i]
            idR, hR = lst[i+1]
            dist = idR - idL
            peak = max(hL, hR) + (dist - abs(hL - hR)) // 2
            ans = max(ans, peak)

        # Last segment to n
        last_id, last_h = lst[-1]
        ans = max(ans, last_h + (n - last_id))
        return ans
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxBuilding(int n, vector<vector<int>>& restrictions) {
        // vector of pairs {id, height} using long long
        vector<pair<long long,long long>> v;
        v.emplace_back(1LL, 0LL);
        for (auto &r : restrictions)
            v.emplace_back(r[0], r[1]);

        sort(v.begin(), v.end());          // sort by id

        int m = v.size();

        // Forward pass
        for (int i = 1; i < m; ++i) {
            long long dist = v[i].first - v[i-1].first;
            long long allowed = v[i-1].second + dist;
            if (v[i].second > allowed) v[i].second = allowed;
        }

        // Backward pass
        for (int i = m-2; i >= 0; --i) {
            long long dist = v[i+1].first - v[i].first;
            long long allowed = v[i+1].second + dist;
            if (v[i].second > allowed) v[i].second = allowed;
        }

        long long ans = 0;

        // Intervals between restrictions
        for (int i = 0; i < m-1; ++i) {
            long long idL = v[i].first, hL = v[i].second;
            long long idR = v[i+1].first, hR = v[i+1].second;
            long long dist = idR - idL;
            long long peak = max(hL, hR) + (dist - llabs(hL - hR)) / 2;
            ans = max(ans, peak);
        }

        // Last segment to building n
        long long last_id = v.back().first, last_h = v.back().second;
        ans = max(ans, last_h + (long long)n - last_id);

        return static_cast<int>(ans);
    }
};
```

---

## 5. Why this solution is *production‑ready*

| Criterion | What we did |
|-----------|-------------|
| **Time‑complexity** | `O(M log M)` – acceptable for `M ≤ 100 000`. |
| **Space‑complexity** | `O(M)` – no need to touch every of the up to 1 billion buildings. |
| **Overflow safety** | All intermediate values use 64‑bit (`long`/`long long`). |
| **Readability** | Variable names like `idL`, `hL`, `peak` make intent obvious. |
| **Testing** | The code handles zero, one, and many restrictions without special casing in the main loop. |
| **Edge‑case safety** | The forward & backward clipping guarantees every pair of consecutive points satisfies the distance rule, eliminating the need for a complex feasibility check later. |

---

## 6. Summary

| Aspect | Take‑away |
|--------|-----------|
| **Good** | Simple greedy sweep, linear scans, clear formula for the interval peak. |
| **Bad** | None—time and memory are optimal. |
| **Ugly** | Careful use of 64‑bit types and virtual points for `n`. |

This pattern of **sorting + two‑sided clipping + interval analysis** appears in many problems (e.g., “Build the Fence”, “Maximum Subarray with Constraints”), and the same reasoning applies: enforce the constraints locally, then compute the global optimum from the clipped boundaries.

---

## 7. How to Apply This Knowledge

* **Interview** – describe the greedy sweep and the interval peak formula in under 5 minutes.  
* **Production code** – add defensive checks (e.g., verify `n > 0`, `restrictions` non‑null).  
* **Testing** – create unit tests covering:  
  * No restrictions.  
  * Single restriction far from both ends.  
  * Multiple overlapping restrictions.  
  * Large `n` with few restrictions.  
  * Cases that force the forward or backward clipping to take effect.

With this foundation you can confidently tackle variations of “building‑height” problems that appear in coding interviews, competitions, and real‑world architectural planning software. Happy coding! 🚀

--- 

**Keywords**: *Maximum Building Height*, *LeetCode 1840*, *Greedy Sweep*, *O(M log M)*, *adjacent difference*, *restriction clipping*, *Java*, *Python*, *C++*, *edge cases*.