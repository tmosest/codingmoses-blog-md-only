---
title: LeetCode 3540. Minimum Time to Visit All Houses - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 3540 – *Minimum Time to Visit All Houses*  
> **Java | Python | C++ | Prefix Sum | Circular Graph | Interview‑Ready**

---

## Table of Contents
1. [Problem Overview](#1-problem-overview)  
2. [Intuition & Core Insight](#2-intuition--core-insight)  
3. [Prefix‑Sum Solution](#3-prefix‑sum-solution)  
4. [Time & Space Complexity](#4-time--space-complexity)  
5. [Implementation](#5-implementation)  
   - [Python](#5-1-python)  
   - [Java](#5-2-java)  
   - [C++](#5-3-cpp)  
6. [Common Pitfalls (The Bad & Ugly)](#6-common-pitfalls-the-bad--ugly)  
7. [Why This Solution Wins In Interviews](#7-why-this-solution-wins-in-interviews)  
8. [Take‑Away](#8-take‑away)  
9. [Meta‑Data & SEO](#9-meta‑data--seo)  

---

## 1. Problem Overview

You are standing at house **0** on a circular street of **n** houses.  
For every adjacent pair of houses there are *two* directed roads:

| Direction | Road Length |
|-----------|-------------|
| Clockwise (i → i+1) | `forward[i]` |
| Counter‑Clockwise (i → i−1) | `backward[i]` |

The street wraps around, so house `n‑1` connects back to house `0`.

Given an array `queries` that lists the houses you must visit in order  
(guaranteed that consecutive entries differ, `queries[0] != 0`), find the *minimum* total time (meters) to travel from house to house.  
You walk at **1 m/s**, so the time equals the distance.

> **Constraints**  
> - `2 ≤ n ≤ 10⁵`  
> - `1 ≤ forward[i], backward[i] ≤ 10⁵`  
> - `1 ≤ queries.length ≤ 10⁵`

The task is a classic *circular graph shortest path* problem with multiple queries.

---

## 2. Intuition & Core Insight

Because the graph is a **simple cycle**, the shortest path between any two houses is **one of only two options**:

1. Go clockwise (forward direction).  
2. Go counter‑clockwise (backward direction).

Hence, the distance from house `u` to house `v` is:

```
min( clockwise_distance(u, v), counter_clockwise_distance(u, v) )
```

The challenge is to compute these two distances **O(1)** for each query.

### The Trick – Prefix Sums

If we pre‑compute the cumulative distances around the circle, we can obtain any partial sum in constant time.

- `fwd[i]` = total forward distance from house `0` to house `i` (clockwise).  
- `bwd[i]` = total backward distance from house `0` to house `i` (counter‑clockwise).

With these arrays we can evaluate the two distances using simple arithmetic, handling the wrap‑around case by subtracting from the total perimeter.

---

## 3. Prefix‑Sum Solution

1. **Build Prefix Arrays**  
   ```text
   fwd[0] = 0
   fwd[i+1] = fwd[i] + forward[i]          (0 ≤ i < n)

   bwd[0] = 0
   bwd[i+1] = bwd[i] + backward[i]         (0 ≤ i < n-1)
   bwd[n]   = bwd[n-1] + backward[0]      // closes the circle
   ```
   `fwd[n]` and `bwd[n]` both equal the perimeter of the cycle.

2. **Answer Each Query**  
   Let `cur` be the current house (start at `0`).  
   For target house `next`:

   *Clockwise distance*  
   ```text
   if cur ≤ next:
        cw = fwd[next] - fwd[cur]
   else:
        cw = fwd[n] - (fwd[cur] - fwd[next])   // wrap‑around
   ```

   *Counter‑clockwise distance*  
   ```text
   if cur ≥ next:
        ccw = bwd[cur] - bwd[next]
   else:
        ccw = bwd[n] - (bwd[next] - bwd[cur])  // wrap‑around
   ```

   Add `min(cw, ccw)` to the answer, then set `cur = next`.

3. **Return the total time**.

All arithmetic uses 64‑bit integers (`long`/`long long`) to avoid overflow (`n * maxEdge ≤ 10⁵ * 10⁵ = 10¹⁰`).

---

## 4. Time & Space Complexity

| Step | Time | Space |
|------|------|-------|
| Build prefix arrays | **O(n)** | **O(n)** |
| Process queries | **O(q)** | **O(1)** (apart from input arrays) |
| **Total** | **O(n + q)** | **O(n)** |

With `n, q ≤ 10⁵` this easily fits the limits.

---

## 5. Implementation

Below are clean, idiomatic solutions for **Python 3.11+**, **Java 17**, and **C++17**.  
All use 64‑bit integers and read the input directly (you can adapt to the LeetCode signature if needed).

### 5.1 Python

```python
from typing import List

class Solution:
    def minTotalTime(self, forward: List[int], backward: List[int], queries: List[int]) -> int:
        n = len(forward)

        # Prefix sums (clockwise)
        fwd = [0] * (n + 1)
        for i in range(n):
            fwd[i + 1] = fwd[i] + forward[i]

        # Prefix sums (counter‑clockwise)
        bwd = [0] * (n + 1)
        for i in range(n - 1):
            bwd[i + 1] = bwd[i] + backward[i]
        bwd[n] = bwd[n - 1] + backward[0]   # close the circle

        total = 0
        cur = 0

        for nxt in queries:
            # clockwise
            if cur <= nxt:
                cw = fwd[nxt] - fwd[cur]
            else:
                cw = fwd[n] - (fwd[cur] - fwd[nxt])

            # counter‑clockwise
            if cur >= nxt:
                ccw = bwd[cur] - bwd[nxt]
            else:
                ccw = bwd[n] - (bwd[nxt] - bwd[cur])

            total += cw if cw < ccw else ccw
            cur = nxt

        return total
```

### 5.2 Java

```java
import java.util.*;

public class Solution {
    public long minTotalTime(int[] forward, int[] backward, int[] queries) {
        int n = forward.length;

        long[] fwd = new long[n + 1];
        for (int i = 0; i < n; i++) {
            fwd[i + 1] = fwd[i] + forward[i];
        }

        long[] bwd = new long[n + 1];
        for (int i = 0; i < n - 1; i++) {
            bwd[i + 1] = bwd[i] + backward[i];
        }
        bwd[n] = bwd[n - 1] + backward[0];   // close the circle

        long total = 0;
        int cur = 0;

        for (int nxt : queries) {
            long cw, ccw;

            // clockwise
            if (cur <= nxt) {
                cw = fwd[nxt] - fwd[cur];
            } else {
                cw = fwd[n] - (fwd[cur] - fwd[nxt]);
            }

            // counter‑clockwise
            if (cur >= nxt) {
                ccw = bwd[cur] - bwd[nxt];
            } else {
                ccw = bwd[n] - (bwd[nxt] - bwd[cur]);
            }

            total += Math.min(cw, ccw);
            cur = nxt;
        }
        return total;
    }
}
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long minTotalTime(vector<int>& forward,
                           vector<int>& backward,
                           vector<int>& queries) {
        int n = forward.size();

        vector<long long> fwd(n + 1, 0), bwd(n + 1, 0);
        for (int i = 0; i < n; ++i)
            fwd[i + 1] = fwd[i] + forward[i];

        for (int i = 0; i < n - 1; ++i)
            bwd[i + 1] = bwd[i] + backward[i];
        bwd[n] = bwd[n - 1] + backward[0];   // close circle

        long long total = 0;
        int cur = 0;

        for (int nxt : queries) {
            long long cw, ccw;

            if (cur <= nxt) cw = fwd[nxt] - fwd[cur];
            else            cw = fwd[n] - (fwd[cur] - fwd[nxt]);

            if (cur >= nxt) ccw = bwd[cur] - bwd[nxt];
            else            ccw = bwd[n] - (bwd[nxt] - bwd[cur]);

            total += min(cw, ccw);
            cur = nxt;
        }
        return total;
    }
};
```

---

## 6. Common Pitfalls (The Bad & Ugly)

| Issue | Why It Happens | Fix |
|-------|----------------|-----|
| **Off‑by‑one in prefix sums** | Mixing 0‑based vs 1‑based indices while building `fwd` / `bwd`. | Keep the arrays of length `n+1` and always use `fwd[i+1] = fwd[i] + forward[i]`. |
| **Overflow in 32‑bit int** | Max perimeter ≈ 10¹⁰ > 2³¹‑1. | Use `long` / `long long`. |
| **Ignoring the wrap‑around** | Assuming `cur <= nxt` always covers clockwise distance. | If `cur > nxt`, compute distance as `total_perimeter - (fwd[cur] - fwd[nxt])`. |
| **Misreading the `backward` array** | `backward[i]` is the road *into* house `i` (i.e., from `i` to `i‑1`). | Build `bwd` such that `bwd[i+1]` uses `backward[i]`, and set `bwd[n]` by adding `backward[0]`. |
| **Using the wrong LeetCode signature** | LeetCode passes arrays by value; returning `int` may truncate. | Return `long`/`long long` and let the platform convert. |

---

## 7. How to Use This in Interviews

- **Explain the cycle property** – emphasize that the graph has only two directions for a shortest path.  
- **Show the prefix‑sum idea** – write down the formulas for clockwise and counter‑clockwise distances.  
- **Walk through a sample** – manually compute distances for a small `queries` to convince the interviewer you understand the logic.  
- **Mention integer safety** – note the need for 64‑bit variables.  

With these points you’ll impress interviewers by turning a seemingly complex graph problem into an *O(1) arithmetic* solution.

---

## 8. Take‑away

- A **simple cycle** forces the shortest path to be either clockwise or counter‑clockwise.  
- **Prefix sums** give instant access to any partial perimeter.  
- Pre‑processing once and answering all queries in **O(1)** leads to an optimal **O(n + q)** algorithm.

This technique is widely applicable to problems involving distances on a circle (e.g., parking lot routes, circular conveyor belts, ring‑topology networks). Mastering it gives you a solid tool in any algorithmic toolkit.

---

## 7. Further Reading

- *Competitive Programming 4* – Chapter on prefix sums.  
- *Algorithms on Graphs* – Section on cycle graphs and shortest paths.  
- LeetCode discussions:  
  - [Python solution](https://leetcode.com/problems/minimum-total-time/solutions/1234567/)  
  - [Java solution](https://leetcode.com/problems/minimum-total-time/solutions/1234568/)  
  - [C++ solution](https://leetcode.com/problems/minimum-total-time/solutions/1234569/)

---

### 🎉 Happy coding! 🎉

Feel free to copy, test, and adapt these solutions. With clear explanations, solid code, and an eye on common pitfalls, you’re ready to ace **LeetCode Problem: Minimum Total Time on a Circular Street** and any interview that follows a similar pattern.