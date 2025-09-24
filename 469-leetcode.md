---
title: LeetCode 469. Convex Polygon - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 469. Convex Polygon – Solution in **Java**, **Python**, and **C++**  
### 🧠 A Quick Recap of the Problem

> You are given a simple polygon defined by an array of points `points[i] = [xi, yi]` that are joined in the order they appear.  
> Return **true** if the polygon is convex, otherwise **false**.  
> *Assume the polygon is always simple (no self‑intersections).*

The key observation:  
A simple polygon is convex **iff** all consecutive turns (cross‑products of edges) have the same sign (all left or all right).  
If any turn changes sign, the polygon has a “dent” and is **not** convex.

Below you’ll find concise, production‑ready implementations in three of the most popular languages for technical interviews.

---

## 🗂️ Code Solutions

| Language | Signature | Implementation |
|----------|-----------|----------------|
| **Java** | `public boolean isConvex(List<List<Integer>> points)` | ✅ |
| **Python** | `def is_convex(points: List[List[int]]) -> bool:` | ✅ |
| **C++** | `bool isConvex(const vector<vector<int>>& points)` | ✅ |

> **Tip**: Always test with edge cases (triangle, collinear points, self‑intersecting polygon – even though the problem guarantees simplicity).

### 1️⃣ Java (LeetCode‑friendly)

```java
import java.util.List;

public class Solution {
    public boolean isConvex(List<List<Integer>> points) {
        int n = points.size();
        if (n < 3) return true;                     // A triangle is always convex
        long sign = 0;                              // 0 → not decided, 1 → positive, -1 → negative

        for (int i = 0; i < n; i++) {
            long dx1 = points.get((i + 1) % n).get(0) - points.get(i).get(0);
            long dy1 = points.get((i + 1) % n).get(1) - points.get(i).get(1);
            long dx2 = points.get((i + 2) % n).get(0) - points.get((i + 1) % n).get(0);
            long dy2 = points.get((i + 2) % n).get(1) - points.get((i + 1) % n).get(1);
            long cross = dx1 * dy2 - dy1 * dx2;     // cross product

            if (cross != 0) {
                long currentSign = cross > 0 ? 1 : -1;
                if (sign == 0) sign = currentSign;
                else if (sign != currentSign) return false;
            }
        }
        return true;
    }
}
```

**Why this works**  
* The cross product `dx1 * dy2 - dy1 * dx2` gives the signed area of the parallelogram formed by two consecutive edges.  
* A positive value means a left turn, negative means right.  
* If all turns have the same sign, the polygon is convex.  

---

### 2️⃣ Python

```python
from typing import List

def is_convex(points: List[List[int]]) -> bool:
    n = len(points)
    if n < 3:
        return True

    sign = 0
    for i in range(n):
        x1, y1 = points[i]
        x2, y2 = points[(i + 1) % n]
        x3, y3 = points[(i + 2) % n]

        cross = (x2 - x1) * (y3 - y2) - (y2 - y1) * (x3 - x2)

        if cross != 0:
            current_sign = 1 if cross > 0 else -1
            if sign == 0:
                sign = current_sign
            elif sign != current_sign:
                return False
    return True
```

*Pythonic features*:  
* Use `% n` to wrap around the polygon.  
* `typing.List` for clearer function signatures.

---

### 3️⃣ C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool isConvex(const vector<vector<int>>& points) {
        int n = points.size();
        if (n < 3) return true;

        long long sign = 0; // 0 → undecided, 1 → positive, -1 → negative
        for (int i = 0; i < n; ++i) {
            long long x1 = points[i][0],      y1 = points[i][1];
            long long x2 = points[(i+1)%n][0], y2 = points[(i+1)%n][1];
            long long x3 = points[(i+2)%n][0], y3 = points[(i+2)%n][1];

            long long cross = (x2 - x1)*(y3 - y2) - (y2 - y1)*(x3 - x2);
            if (cross != 0) {
                long long cur = cross > 0 ? 1 : -1;
                if (sign == 0) sign = cur;
                else if (sign != cur) return false;
            }
        }
        return true;
    }
};
```

*Why we use `long long`* – the coordinates can reach `±10^4`; multiplying them can overflow 32‑bit ints.

---

## 📈 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` – one pass over the points | `O(n)` | `O(n)` |
| **Space** | `O(1)` – only a few variables | `O(1)` | `O(1)` |

*The algorithm is optimal: you cannot determine convexity without examining every vertex.*

---

## 🧪 Edge‑Case Checklist

| Case | Why it matters |
|------|----------------|
| **Collinear edges** – e.g., `(0,0)-(1,1)-(2,2)` | Cross product = 0 → ignore, still convex if no sign change |
| **Three points (triangle)** – always convex | Handles degenerate minimal polygons |
| **Large coordinates** | Use 64‑bit integers to avoid overflow |
| **Re‑ordered points** | Algorithm works for any cyclic order, including reversed direction |

---

## ❌ Common Pitfalls (“The Bad”)

| Mistake | Consequence |
|---------|-------------|
| Using `int` for cross product | Overflow on large coordinates → incorrect result |
| Ignoring wrap‑around (`% n`) | Out‑of‑range indices on last vertices |
| Returning `false` on first zero cross product | Wrongly rejects convex polygons with collinear points |
| Not handling `n < 3` | Index errors when input has only 2 points (though problem guarantees ≥3) |

---

## 🎯 “The Ugly” – What Can Go Wrong

1. **Precision errors in floating‑point implementation**  
   – Use integers whenever possible.  
   – If you must use `float/double`, compare with a tolerance (e.g., `abs(cross) < 1e-9`).

2. **Complexity over‑engineering**  
   – Writing a full convex‑hull algorithm (Graham Scan / Andrew’s monotone chain) for a simple check is overkill and obscures the solution.

3. **Ignoring input guarantees**  
   – The problem guarantees a *simple* polygon. Assuming self‑intersections can waste time checking unnecessary edge intersections.

---

## 🚀 How This Helps in a Tech Interview

1. **Shows mastery of geometry** – Cross products are a classic interview technique.  
2. **Demonstrates clean, time‑efficient coding** – `O(n)` time, `O(1)` space.  
3. **Highlights defensive programming** – handling edge cases, overflow.  
4. **Exhibits multilingual proficiency** – Providing Java, Python, C++ shows you can adapt to a team’s stack.

---

## 📚 Final Thoughts

Convex Polygon (LeetCode 469) is a deceptively simple problem that turns into a great interview showcase:

- **Good**: Straight‑forward cross‑product logic, optimal O(n) time, constant space.  
- **Bad**: Overflow, wrong index handling, unnecessary complexity.  
- **Ugly**: Precision pitfalls, over‑engineering.

Mastering this problem not only earns you a “✓” on your LeetCode streak but also proves you can **reason geometrically**, **write production‑ready code**, and **communicate clear solutions** – all of which recruiters love.

---

### 📌 SEO‑Optimized Takeaway

- **Keywords**: Convex Polygon, LeetCode 469, Cross Product, O(n) algorithm, Java solution, Python solution, C++ solution, interview prep, data structures, algorithms, job interview tips, tech interview success.  
- **Meta description** (for a blog post): “Learn how to solve LeetCode 469 Convex Polygon in Java, Python, and C++ with an optimal O(n) algorithm. Discover common pitfalls, edge cases, and interview‑ready tips to ace your next tech job.”  

Share this post on LinkedIn, Medium, or your portfolio site and watch recruiters notice your problem‑solving chops! 🚀

---