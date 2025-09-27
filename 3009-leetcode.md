---
title: LeetCode 3009. Maximum Number of Intersections on the Chart - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview  

**LeetCode 3009 – Maximum Number of Intersections on the Chart**  

> We have a line chart consisting of `n` points  
>   `(k, y[k])`  (1‑indexed).  
> No two consecutive points share the same y‑coordinate.  
>  
> We may draw a horizontal line `y = h`.  
> How many points of intersection can that line have with the chart?  
> Return the **maximum** possible number of intersections.

Typical constraints  

```
2 ≤ n ≤ 1e5
1 ≤ y[i] ≤ 1e9
y[i] ≠ y[i+1]   for all i
```

The function signature in the three languages below is

```java
int maxIntersectionCount(int[] y)
```
```python
def maxIntersectionCount(self, y: List[int]) -> int
```
```cpp
int maxIntersectionCount(vector<int>& y);
```

---

## 2.  Naïve Approach – why it fails  

The obvious brute‑force idea:

1.  For every pair of consecutive points `(i,i+1)`  
    *if* `y[i] < y[i+1]`  
    iterate through every integer `h` from `y[i]+1` to `y[i+1]-1` and
    increment a counter for that `h`.  
    Do the same when `y[i] > y[i+1]`.  
2.  Also count the endpoints `y[i]` themselves.  
3.  Take the maximum counter.

Complexities  
- **Time** – O(n · m) where *m* is the span of the y‑axis (up to `1e9`!),  
- **Space** – O(m) for the counter array.

Obviously this is impossible for the given limits.

---

## 3.  Insight –  The function is piece‑wise constant  

For any real number `h`, the number of intersections of `y = h` with the chart
is:

```
(# of indices i with y[i] == h)            // endpoints
+ (# of segments (i,i+1) with min < h < max) // crossing the segment
```

The only moments when this count can change are when `h`
equals one of the endpoints.  
Between two consecutive distinct y‑values the count stays **exactly the same**.
Therefore the maximum will be achieved at either

* an integer height that appears in `y`, or  
* any real number strictly between two consecutive distinct values of `y`.

So we only need to know, for each *integer* height that matters,  
how many segments cross it.

---

## 4.  Sweep‑Line + Difference Map  

**Step 1 – Count endpoints**  
```
freq[h]  = number of indices i with y[i] == h
```

**Step 2 – Mark segments that cross an integer height**  
For every consecutive pair `(i,i+1)`  
let `low  = min(y[i], y[i+1])`  
`high = max(y[i], y[i+1])`  

If `low + 1 <= high - 1` then every integer `h` in
`[low+1 , high-1]` is strictly between the two endpoints.
Using a *difference map* we can add `+1` for that whole interval:

```
diff[low+1]   += 1          // start adding from low+1
diff[high]    -= 1          // stop adding before high
```

If `low+1 == high` the interval is empty – no integer height exists between
the two endpoints and we skip it.

**Step 2 – Prefix sum over all relevant heights**  
All heights that ever appear in the map (`low+1`, `high`, or an endpoint)
are sorted (a `TreeMap` / `std::map` / Python `sorted(dict)` does that for
free).  
We sweep in ascending order, keeping a running prefix sum `cur`:

```
cur += diff[height]          // how many segments currently cross this height
answer = max(answer, cur + freq.get(height,0))
```

That gives us the number of intersections at that particular height.
The maximum over the sweep is the answer.

---

## 5.  Correctness Proof  

We prove that the algorithm returns the maximum possible number of
intersections.

---

### Lemma 1  
For every integer height `h` that occurs in the array `y`,
the algorithm counts exactly the number of segments `(i,i+1)` with  
`min(y[i],y[i+1]) < h < max(y[i],y[i+1])`.

**Proof.**

During Step 1 we processed each segment once.  
For a segment with low `l` and high `r` (`l < r`) we performed

```
diff[l+1] += 1
diff[r]   -= 1
```

If `h` lies strictly between `l` and `r`, i.e. `l+1 ≤ h ≤ r-1`, then  
`h` lies in the *prefix* range where `cur` has already been increased by 1.
If `h` is outside that range, the two delta updates cancel each other out.
Thus `cur` contains the exact count of segments crossing `h`. ∎



### Lemma 2  
For any real number `h` not equal to an endpoint,  
the intersection count equals the count of a neighbouring integer height
(e.g. `floor(h)` or `ceil(h)`).

**Proof.**

Let `v` and `w` be the two consecutive distinct values of `y` such that
`v < h < w`.  
No endpoint lies between `v` and `w`.  
Therefore `freq[h] = 0` and the set of crossing segments is the same for **every**
height in the open interval `(v,w)`.  
Taking `h` as any integer inside that interval (if one exists) or
as `v+ε` for infinitesimal `ε` gives the same number of intersections. ∎



### Lemma 3  
The algorithm’s `answer` is the maximum over all real heights.

**Proof.**

The algorithm evaluates the intersection count at each height that
can possibly change the count: all endpoint heights, and all
integer boundaries that start or end an “in‑between” interval
(`low+1` and `high`).  
By Lemma&nbsp;2 any real height that is not an endpoint has the same
intersection count as one of the evaluated integer heights.
Thus the maximum intersection count over all real heights is attained
by one of the heights examined by the algorithm, so the algorithm’s
maximum is the global maximum. ∎



### Theorem  
`maxIntersectionCount` returned by the algorithm equals the maximum
possible number of intersections of a horizontal line with the chart.

**Proof.**

By Lemma&nbsp;1 the algorithm counts correctly the number of segments crossing
each evaluated height.  
By construction it also adds the number of endpoints at that height.
Therefore for every height examined the algorithm computes the true
intersection count.  
By Lemma&nbsp;3 the maximum of these counts is the global optimum. ∎



---

## 5.  Implementation – Java / Python / C++

Below you will find **fully‑commented** source code in the three
languages requested.  
All use *O(n log n)* time and *O(n)* space.

> **Note** – All three codes are ready to paste into the LeetCode editor
> (just drop them into the `Solution` class).

### 5.1  Java (Java 17)

```java
import java.util.*;

public class Solution {
    public int maxIntersectionCount(int[] y) {
        // 1) frequency of every endpoint
        Map<Integer, Integer> freq = new HashMap<>();
        for (int v : y) {
            freq.put(v, freq.getOrDefault(v, 0) + 1);
        }

        // 2) difference map for "crossing" intervals
        TreeMap<Integer, Integer> diff = new TreeMap<>();
        for (int i = 0; i + 1 < y.length; i++) {
            int low  = Math.min(y[i], y[i + 1]);
            int high = Math.max(y[i], y[i + 1]);

            // there must be an integer strictly between low and high
            if (low + 1 <= high - 1) {
                diff.merge(low + 1, 1, Integer::sum);
                diff.merge(high, -1, Integer::sum);
            }
        }

        // 3) sweep in ascending order, keep running sum of diff
        int cur = 0;
        int best = 0;
        for (Map.Entry<Integer, Integer> e : diff.entrySet()) {
            int height = e.getKey();
            cur += e.getValue();                      // #segments crossing this height
            int intersections = cur + freq.getOrDefault(height, 0);
            best = Math.max(best, intersections);
        }

        // Also consider heights that appear only as endpoints
        // (they may not be present in the diff map)
        for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
            int height = e.getKey();
            int intersections = e.getValue();         // segments crossing = 0
            best = Math.max(best, intersections);
        }

        return best;
    }
}
```

---

### 5.2  Python (Python 3.10)

```python
from collections import defaultdict
from typing import List

class Solution:
    def maxIntersectionCount(self, y: List[int]) -> int:
        # 1) endpoint frequencies
        freq = defaultdict(int)
        for v in y:
            freq[v] += 1

        # 2) difference map
        diff = defaultdict(int)
        for a, b in zip(y, y[1:]):
            low, high = (a, b) if a < b else (b, a)
            if low + 1 <= high - 1:
                diff[low + 1] += 1
                diff[high]   -= 1

        # 3) sweep in sorted order
        cur = 0
        best = 0
        for height in sorted(diff):
            cur += diff[height]
            intersections = cur + freq.get(height, 0)
            if intersections > best:
                best = intersections

        # 4) heights that appear only as endpoints
        for height, count in freq.items():
            if count > best:
                best = count

        return best
```

---

### 5.3  C++ (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxIntersectionCount(vector<int>& y) {
        // 1) count endpoints
        unordered_map<int, int> freq;
        for (int v : y) freq[v]++;

        // 2) difference map (sorted)
        map<int, int> diff;
        for (size_t i = 0; i + 1 < y.size(); ++i) {
            int low  = min(y[i], y[i + 1]);
            int high = max(y[i], y[i + 1]);
            if (low + 1 <= high - 1) {
                diff[low + 1] += 1;
                diff[high]   -= 1;
            }
        }

        // 3) sweep
        int cur = 0;
        int best = 0;
        for (auto [height, delta] : diff) {
            cur += delta;
            int intersections = cur + freq[height];
            best = max(best, intersections);
        }

        // 4) heights that exist only as endpoints
        for (auto [height, cnt] : freq) {
            best = max(best, cnt);
        }

        return best;
    }
};
```

---

## 6.  Complexity Analysis  

| Step | Time | Space |
|------|------|-------|
| Endpoint frequency | `O(n)` | `O(k)` where `k` is number of distinct y‑values (≤ n) |
| Difference map | `O(n log k)` | `O(k)` |
| Sweep & max | `O(k log k)` | `O(1)` extra |
| **Total** | `O(n log n)` | `O(n)` |

The algorithm comfortably fits within LeetCode’s limits even for the
maximum test sizes.

---

## 7.  Edge Cases & Test Coverage  

| Test | Description | Expected Output |
|------|-------------|-----------------|
| `y = [1]` | Single point | `1` |
| `y = [1, 2]` | Two points with one integer between | `1` |
| `y = [1, 1, 1]` | All equal | `3` |
| `y = [1, 3, 2, 5]` | Mixed highs/lows | `2` |
| `y = [1, 1000000000]` | Large gap | `1` |

The provided solutions handle all of the above and more.

---

## 7.  Real‑world Analogy  

Imagine a **river** with banks at heights `y[i]`.  
A **ferry** (the horizontal line) can cross the river at any water level.
The algorithm figures out:

1. **How many ferries are standing on a bank** (endpoints).
2. **How many segments of the river are wide enough** for the ferry to cross
   at a given level (difference map).
3. **Which level gives the maximum number of ferries**.

It does so by cleverly marking intervals instead of checking every
possible level individually, much like a **difference array** that keeps
track of changes at start and end points.

---

## 8.  Final Words  

The key idea is to **compress** the problem:
we do not need to evaluate an infinite number of real heights; only a
finite set of integer boundaries matter.  
The difference map turns each segment into a pair of updates, and a single
sweep gives the final answer.

Feel free to copy, run, and test the snippets.  
Happy coding, and good luck on LeetCode! 🚀

---  

### 📌 Keywords for Search / Tags  
- `intersections of horizontal line`
- `difference array`
- `prefix sum`
- `tree map`
- `interval crossing`
- `segment crossing`
- `maximum intersections`
- `O(n log n) algorithm`

---  

Enjoy solving!