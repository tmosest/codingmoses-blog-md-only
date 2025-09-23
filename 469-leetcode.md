---
title: LeetCode 469. Convex Polygon - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🔍 LeetCode 469 – Convex Polygon  
### Java | Python | C++ – Full Code + SEO‑Optimized Blog Post  

---

## 1️⃣ Problem Recap  

> **Convex Polygon**  
> You’re given a list of 2‑D points `points = [[x₁,y₁],[x₂,y₂],…]` that form a simple polygon when connected in order.  
> **Return `true` if the polygon is convex, otherwise return `false`.**  
>  
> **Constraints**  
> * `3 ≤ points.length ≤ 10⁴`  
> * All points are unique and the polygon is simple (no self‑intersections).  

---

## 2️⃣ Algorithm – Cross Product Sign Test  

A polygon is convex iff all turns between consecutive edges are **consistently clockwise or counter‑clockwise**.  
For three consecutive vertices `A → B → C` the signed area of the parallelogram (cross product) tells the turn direction:

```
cross = (B.x - A.x) * (C.y - B.y) - (B.y - A.y) * (C.x - B.x)
```

* `cross > 0` → counter‑clockwise turn  
* `cross < 0` → clockwise turn  
* `cross == 0` → collinear (still fine for convexity)

Traverse all vertices (including the wrap‑around `points[0]` and `points[1]` after the last vertex).  
If at any point the sign changes (positive ↔ negative) the polygon is **not** convex.

**Time Complexity**: `O(n)`  
**Space Complexity**: `O(1)` (in‑place)

---

## 3️⃣ Edge‑Case Checklist  

| Scenario | What to Watch For |
|----------|-------------------|
| **Collinear edges** | Cross product = 0 – ignore, keep the current sign. |
| **All points on a straight line** | All cross products 0 → convex (degenerate case). |
| **Large coordinate values** | Use 64‑bit integers (`long` / `long long`) to avoid overflow. |
| **Polygon with 3 points** | Always convex (triangle). |

---

## 4️⃣ Code Implementations  

### 4.1 Java

```java
import java.util.List;

public class Solution {
    public boolean isConvex(List<List<Integer>> points) {
        int n = points.size();
        if (n < 4) return true;          // triangle is always convex

        int sign = 0;                     // 0 = unknown, 1 = positive, -1 = negative

        for (int i = 0; i < n; i++) {
            int[] p0 = points.get(i);
            int[] p1 = points.get((i + 1) % n);
            int[] p2 = points.get((i + 2) % n);

            long cross = (long)(p1.get(0) - p0.get(0)) * (p2.get(1) - p1.get(1))
                       - (long)(p1.get(1) - p0.get(1)) * (p2.get(0) - p1.get(0));

            if (cross != 0) {
                int current = cross > 0 ? 1 : -1;
                if (sign == 0) {
                    sign = current;
                } else if (sign != current) {
                    return false;        // sign change → concave
                }
            }
        }
        return true;
    }
}
```

> **Why `long`?**  
> The product of two 10⁴‑sized coordinates can reach 10⁸, fitting in 32‑bit, but intermediate calculations may overflow if the compiler promotes to 32‑bit. Using `long` guarantees safety.

---

### 4.2 Python

```python
from typing import List

class Solution:
    def isConvex(self, points: List[List[int]]) -> bool:
        n = len(points)
        if n < 4:                         # triangle is always convex
            return True

        sign = 0   # 0 = unknown, 1 = positive, -1 = negative

        for i in range(n):
            p0 = points[i]
            p1 = points[(i + 1) % n]
            p2 = points[(i + 2) % n]

            cross = (p1[0] - p0[0]) * (p2[1] - p1[1]) - \
                    (p1[1] - p0[1]) * (p2[0] - p1[0])

            if cross:
                cur = 1 if cross > 0 else -1
                if sign == 0:
                    sign = cur
                elif sign != cur:
                    return False
        return True
```

---

### 4.3 C++

```cpp
#include <vector>

class Solution {
public:
    bool isConvex(const std::vector<std::vector<int>>& points) {
        int n = points.size();
        if (n < 4) return true;           // triangle is convex

        int sign = 0;                     // 0 = unknown, 1 = positive, -1 = negative

        for (int i = 0; i < n; ++i) {
            const auto& p0 = points[i];
            const auto& p1 = points[(i + 1) % n];
            const auto& p2 = points[(i + 2) % n];

            long long cross =
                1LL * (p1[0] - p0[0]) * (p2[1] - p1[1]) -
                1LL * (p1[1] - p0[1]) * (p2[0] - p1[0]);

            if (cross != 0) {
                int cur = (cross > 0) ? 1 : -1;
                if (sign == 0) {
                    sign = cur;
                } else if (sign != cur) {
                    return false;           // sign change -> concave
                }
            }
        }
        return true;
    }
};
```

> **Why `1LL`?**  
> Explicitly cast to 64‑bit to avoid overflow when multiplying int values.

---

## 5️⃣ Blog Article – “The Good, The Bad, The Ugly of Convex Polygon Checks”

### 5.1 Title & Meta (SEO)

**Title** – *“LeetCode 469 – Convex Polygon: Java / Python / C++ Solution + Interview‑Ready Guide”*  
**Meta Description** – *“Learn how to solve LeetCode 469 (Convex Polygon) in Java, Python, and C++. Get a clear algorithm, edge‑case checklist, and interview tips.”*  
**Keywords** – LeetCode 469, Convex Polygon, convexity test, cross product, Java solution, Python solution, C++ solution, algorithm interview, software engineer interview, coding challenge.

---

### 5.2 Introduction  

When interviewers drop a “convex polygon” question, they’re testing more than just your coding skills. They’re looking for:

1. **Mathematical intuition** – understanding geometry in code.  
2. **Algorithmic elegance** – a solution that’s both linear and clean.  
3. **Robustness** – handling collinearity, large coordinates, and wrap‑around indices.  

Below we dissect the problem, reveal the cross‑product trick, and walk through a production‑ready solution in **Java**, **Python**, and **C++**.

---

### 5.3 The Good – Why the Cross‑Product Test Rocks  

* **Linear time** – O(n) traverses the vertices once.  
* **Constant memory** – no auxiliary arrays, just a sign flag.  
* **Deterministic** – no random or heuristic checks.  
* **Intuitive** – a single signed area tells you whether you’re turning left or right.

---

### 5.4 The Bad – Common Pitfalls  

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| **Overflow** | Multiplying two 10⁴ coordinates can overflow 32‑bit ints in Java/C++/Python’s default int (though Python’s int is unbounded). | Use 64‑bit (`long` / `long long`) in Java/C++; Python handles big ints automatically. |
| **Collinearity** | A sequence of points on a straight line returns `cross == 0`. | Treat 0 as “no direction change”; keep the current sign. |
| **Wrap‑around index** | Forgetting to loop from the last vertex back to the first two points. | Use modulo `n` or explicitly handle the last three vertices. |
| **Triangles** | Some naive solutions mis‑label triangles as concave due to missing sign initialization. | If `n < 4`, immediately return `true`. |

---

### 5.5 The Ugly – Things That Can Go Wrong in Real Code  

* **Assuming the input is always valid** – but interviewers sometimes tweak the constraints.  
* **Not accounting for duplicate points** – though the problem states all points are unique.  
* **Using floating‑point cross products** – introducing precision errors.  
* **Omitting time‑complexity analysis** – interviewers often ask for it.  

---

### 5.6 Step‑by‑Step Walkthrough  

1. **Iterate through every trio of consecutive vertices** (`i`, `i+1`, `i+2`), wrapping around with `% n`.  
2. **Compute the cross product** of vectors `(p1 - p0)` and `(p2 - p1)`.  
3. **Determine the sign** of the cross product.  
4. **Initialize the sign** on the first non‑zero cross product.  
5. **Detect a sign change** – if a later cross product has the opposite sign, return `false`.  
6. **If no sign change is found**, the polygon is convex; return `true`.

---

### 5.7 Complexity Analysis  

* **Time** – `O(n)` (one pass over the vertices).  
* **Space** – `O(1)` (only a few integer variables).  

---

### 5.8 Interview Take‑away  

When asked “How do you check if a polygon is convex?” mention:

1. **Geometric insight** – turns are captured by cross product sign.  
2. **Linear algorithm** – single scan, constant memory.  
3. **Edge‑case handling** – collinear edges, wrap‑around, big numbers.  
4. **Proof of correctness** – all turns must have the same orientation.

This demonstrates both your analytical thinking and your ability to write clean, production‑ready code.

---

### 5.9 Final Thoughts  

Convex polygon checks are a classic interview staple because they blend geometry with algorithm design. Mastering the cross‑product sign trick gives you a powerful tool for any question that involves orientation or convexity.  

Use the code snippets above in your portfolio, share them on GitHub, and sprinkle the keywords in your resume and LinkedIn profile to attract recruiters looking for strong algorithmic talent.

---

**Happy coding – and good luck landing that dream job!**