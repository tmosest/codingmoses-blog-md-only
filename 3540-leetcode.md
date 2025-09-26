---
title: LeetCode 3540. Minimum Time to Visit All Houses - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem Overview – Minimum Time to Visit All Houses  

> **LeetCode 3540 – Minimum Time to Visit All Houses**  
> **Difficulty:** Medium  

You have `n` houses arranged in a circle.  
For each adjacent pair of houses we know **two** directed road lengths:

| Direction | Edge list | Length array |
|-----------|-----------|--------------|
| Clockwise (forward) | `i → i+1` (and `n‑1 → 0`) | `forward[i]` |
| Counter‑clockwise (backward) | `i → i-1` (and `0 → n‑1`) | `backward[i]` |

You walk at **1 m/s**. Starting from house `0`, you must visit houses in the exact order given by `queries`.  
`queries[0]` is never `0` and no two consecutive queries are the same.

**Goal** – return the minimal total time to finish the trip.

---

## 🧠 Intuition – Shortest Path on a Weighted Cycle

On a simple circle every pair of vertices has **exactly two** simple paths:

1. **Clockwise** (forward) – sum of the forward edge weights along the way.
2. **Counter‑clockwise** (backward) – sum of the backward edge weights along the way.

The shortest distance between two houses is just the minimum of those two sums.  
So the problem reduces to:

```
total = Σ  min( distance_clockwise(cur, nxt),
                distance_counterclockwise(cur, nxt) )
```

The trick is to compute each distance in O(1). That’s where **prefix sums** shine.

---

## 🔧 Prefix‑Sum Solution

### 1. Build two prefix‑sum arrays

| Array | Meaning | Construction |
|-------|---------|--------------|
| `fwd[i]` | Total forward distance from house `0` to house `i` (exclusive). | `fwd[i+1] = fwd[i] + forward[i]` |
| `bwd[i]` | Total backward distance from house `0` to house `i` (exclusive). | `bwd[i+1] = bwd[i] + backward[i]` |

Both arrays have length `n+1`.  
`fwd[n]` is the whole circumference clockwise, `bwd[n]` the whole circumference counter‑clockwise (they are equal).

### 2. Distance between two houses `u` → `v`

*Clockwise* (moving forward):

```
if u <= v:   cw = fwd[v] - fwd[u]
else:        cw = fwd[n] - (fwd[u] - fwd[v])
```

*Counter‑clockwise* (moving backward):

```
if u >= v:   ccw = bwd[u] - bwd[v]
else:        ccw = bwd[n] - (bwd[v] - bwd[u])
```

Both formulas are O(1).

### 3. Iterate over queries

Keep a `cur` pointer (starting at 0).  
For each next house `q`:

```
total += min(cw(cur, q), ccw(cur, q))
cur = q
```

Return `total` at the end.

**Complexity**

| Operation | Time | Space |
|-----------|------|-------|
| Building prefix sums | `O(n)` | `O(n)` |
| Processing queries | `O(queries.length)` | – |
| Total | `O(n + q)` | `O(n)` |

Both constraints (`n, q ≤ 10⁵`) are easily satisfied.

---

## 🖥️ Code Implementations

Below you’ll find clean, idiomatic implementations in **Java**, **Python**, and **C++**.  
All use the same prefix‑sum logic described above.

---

### Java

```java
import java.util.*;

class Solution {
    public long minTotalTime(int[] forward, int[] backward, int[] queries) {
        int n = forward.length;
        long[] fwd = new long[n + 1];
        long[] bwd = new long[n + 1];

        // Build prefix sums
        for (int i = 0; i < n; i++) {
            fwd[i + 1] = fwd[i] + forward[i];
            bwd[i + 1] = bwd[i] + backward[i];
        }

        long total = 0;
        int cur = 0;

        for (int q : queries) {
            long cw = (cur <= q) ? fwd[q] - fwd[cur]
                                 : fwd[n] - (fwd[cur] - fwd[q]);

            long ccw = (cur >= q) ? bwd[cur] - bwd[q]
                                   : bwd[n] - (bwd[q] - bwd[cur]);

            total += Math.min(cw, ccw);
            cur = q;
        }
        return total;
    }
}
```

---

### Python

```python
from typing import List

class Solution:
    def minTotalTime(self, forward: List[int], backward: List[int], queries: List[int]) -> int:
        n = len(forward)

        # Prefix sums
        fwd = [0] * (n + 1)
        bwd = [0] * (n + 1)
        for i in range(n):
            fwd[i + 1] = fwd[i] + forward[i]
            bwd[i + 1] = bwd[i] + backward[i]

        total = 0
        cur = 0

        for q in queries:
            if cur <= q:
                cw = fwd[q] - fwd[cur]
            else:
                cw = fwd[n] - (fwd[cur] - fwd[q])

            if cur >= q:
                ccw = bwd[cur] - bwd[q]
            else:
                ccw = bwd[n] - (bwd[q] - bwd[cur])

            total += min(cw, ccw)
            cur = q

        return total
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long minTotalTime(vector<int>& forward, vector<int>& backward,
                           vector<int>& queries) {
        int n = forward.size();

        vector<long long> fwd(n + 1, 0), bwd(n + 1, 0);
        for (int i = 0; i < n; ++i) {
            fwd[i + 1] = fwd[i] + forward[i];
            bwd[i + 1] = bwd[i] + backward[i];
        }

        long long total = 0;
        int cur = 0;

        for (int q : queries) {
            long long cw = (cur <= q) ? fwd[q] - fwd[cur]
                                      : fwd[n] - (fwd[cur] - fwd[q]);

            long long ccw = (cur >= q) ? bwd[cur] - bwd[q]
                                       : bwd[n] - (bwd[q] - bwd[cur]);

            total += min(cw, ccw);
            cur = q;
        }
        return total;
    }
};
```

---

## 📚 Blog Article – “The Good, The Bad, and the Ugly of Solving LeetCode 3540”

### 1. The Good – Why This Problem is a Gem

* **Conceptual Simplicity** – Once you realize you’re just walking on a weighted cycle, the whole puzzle collapses into “take the shorter of two sums.”  
* **Reusability** – The prefix‑sum technique is a staple for any circular distance problem, from GPS route planning to network latency minimization.  
* **Scalable Performance** – O(n + q) time and O(n) space work comfortably for the 10⁵ limits – no need for exotic data structures or heavy‑weight algorithms.

### 2. The Bad – Things That Can Trip You Up

| Common Pitfall | Why it Happens | Fix |
|----------------|----------------|-----|
| **Off‑by‑one in prefix sums** | Forgetting that `fwd[i]` is *exclusive* of node `i`. | Explicitly initialize `fwd[0] = 0` and compute `fwd[i+1]` from `fwd[i]`. |
| **Mixing forward and backward indices** | Backward prefix needs to use `backward[i]` where `i` points to the *target* of the backward edge, not the source. | Use the same indexing as the forward array; `bwd[i+1] = bwd[i] + backward[i]`. |
| **Not handling wrap‑around** | Direct subtraction fails when `u > v` or `u < v`. | Use the full circumference (`fwd[n]` or `bwd[n]`) to adjust the difference. |
| **Integer overflow** | Distances up to 10⁵, but `n` can also be 10⁵ → max sum ~10¹⁰. | Use 64‑bit integers (`long` in Java, `long long` in C++, `int` is safe in Python). |

### 3. The Ugly – Advanced Edge Cases & Gotchas

| Edge Case | What to Watch | How to Handle |
|-----------|---------------|---------------|
| **Queries with back‑to‑back wrap‑around** | `cur = n‑1`, `q = 0` | The wrap‑around formula still works because `fwd[n]` is the whole circle. |
| **Unequal forward/backward weights** | The clockwise and counter‑clockwise sums may differ drastically. | Still use `min(cw, ccw)` – no extra checks needed. |
| **Large input size** | Reading input efficiently (especially in Java). | Use `BufferedReader` + `StringTokenizer` or `FastScanner`. |
| **Potential negative indices** | If you accidentally use `backward[0]` for the backward edge from `0` to `n-1`. | Our construction automatically uses `backward[0]` when `i = n-1` in the loop. |

---

## 🚀 SEO‑Optimized Conclusion – Land Your Next Job

> **Want to impress hiring managers on technical interviews?**  
> Mastering LeetCode’s “Minimum Time to Visit All Houses” showcases several high‑value skills:

1. **Algorithmic thinking** – spotting the cycle structure and reducing to two path sums.  
2. **Prefix‑sum mastery** – a classic technique that appears in many interviews.  
3. **Attention to edge‑case detail** – handling wrap‑around, 64‑bit arithmetic, and index consistency.  
4. **Clean, language‑agnostic code** – we’ve shown Java, Python, and C++ solutions for maximum coverage.

Include this problem (and the clean code snippets above) in your portfolio or GitHub. Add a short explanation of your thought process and complexity analysis. Recruiters will instantly see that you can:

* Translate a real‑world scenario into a clean algorithm.  
* Optimize for both time and space.  
* Write robust, production‑ready code in multiple languages.

Good luck – go code! 🧑‍💻🏃‍♂️💼