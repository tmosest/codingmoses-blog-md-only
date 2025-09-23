---
title: LeetCode 469. Convex Polygon - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 469. Convex Polygon  
**Difficulty:** Medium  

> **Problem:**  
> Given a list of points that form a simple polygon when connected in order, determine whether the polygon is convex.  
> A polygon is convex if every internal angle is strictly less than 180°, or equivalently if the cross‑product of consecutive edges never changes sign.  

> **Input:** `List<List<Integer>> points` – at least 3 points, all distinct, no self‑intersection.  
> **Output:** `boolean` – `true` if the polygon is convex, otherwise `false`.

---

## Algorithm – Cross Product Sign Test  

1. **Iterate through each triplet of consecutive vertices**  
   - Let the vertices be `p[i]`, `p[i+1]`, `p[i+2]` (indices modulo `n`).  
   - Compute vectors  
     ```
     a = p[i+1] - p[i]
     b = p[i+2] - p[i+1]
     ```
2. **Compute the cross product**  
   ```
   cross = a.x * b.y - a.y * b.x
   ```
   - `cross > 0`  → left turn (counter‑clockwise).  
   - `cross < 0`  → right turn (clockwise).  
   - `cross == 0` → collinear (ignored – still convex as long as sign never flips).
3. **Track the sign**  
   - Store the first non‑zero sign (`initialSign`).  
   - For every subsequent non‑zero `cross`, if its sign differs from `initialSign`, the polygon is **not** convex → return `false`.  
4. If we finish the loop without a sign flip → return `true`.

**Complexity**

| Step | Time | Space |
|------|------|-------|
| Iteration | `O(n)` | `O(1)` |
| Cross product | `O(1)` per iteration | `O(1)` |

`n` is the number of points (≤ 10⁴).  

**Why use `long` for the cross product?**  
The coordinates are up to `10⁴`, so the product can be as large as `10⁸`. Multiplying two such numbers gives `10¹⁶`, which fits into a 64‑bit signed integer (`long` in Java/C++, `int` in Python).  

---

## Implementation

Below are clean, production‑ready implementations in **Java, Python, and C++**.

### Java

```java
import java.util.List;

public class Solution {
    public boolean isConvex(List<List<Integer>> points) {
        int n = points.size();
        if (n < 3) return false;          // not a polygon

        long initialSign = 0; // 0 = unset, 1 = positive, -1 = negative

        for (int i = 0; i < n; i++) {
            int[] p0 = points.get(i);
            int[] p1 = points.get((i + 1) % n);
            int[] p2 = points.get((i + 2) % n);

            long cross = crossProduct(p0, p1, p2);
            if (cross == 0) continue;   // collinear, ignore

            long sign = cross > 0 ? 1 : -1;
            if (initialSign == 0) {
                initialSign = sign;
            } else if (initialSign != sign) {
                return false; // sign flip → non‑convex
            }
        }
        // If all turns were collinear, the polygon degenerates – still convex
        return true;
    }

    private long crossProduct(int[] a, int[] b, int[] c) {
        // (b - a) × (c - b)
        long abx = b[0] - a[0];
        long aby = b[1] - a[1];
        long bcx = c[0] - b[0];
        long bcy = c[1] - b[1];
        return abx * bcy - aby * bcx;
    }
}
```

### Python

```python
from typing import List

class Solution:
    def isConvex(self, points: List[List[int]]) -> bool:
        n = len(points)
        if n < 3:
            return False

        initial_sign = 0  # 0 = unset, 1 = positive, -1 = negative

        for i in range(n):
            p0 = points[i]
            p1 = points[(i + 1) % n]
            p2 = points[(i + 2) % n]

            cross = self._cross(p0, p1, p2)
            if cross == 0:
                continue  # collinear edge

            sign = 1 if cross > 0 else -1
            if initial_sign == 0:
                initial_sign = sign
            elif initial_sign != sign:
                return False  # sign flip → non‑convex

        return True

    @staticmethod
    def _cross(a: List[int], b: List[int], c: List[int]) -> int:
        # (b - a) × (c - b)
        abx, aby = b[0] - a[0], b[1] - a[1]
        bcx, bcy = c[0] - b[0], c[1] - b[1]
        return abx * bcy - aby * bcx
```

### C++

```cpp
#include <vector>

class Solution {
public:
    bool isConvex(const std::vector<std::vector<int>>& points) {
        int n = points.size();
        if (n < 3) return false;

        long long initialSign = 0; // 0 = unset, 1 = positive, -1 = negative

        for (int i = 0; i < n; ++i) {
            const auto &p0 = points[i];
            const auto &p1 = points[(i + 1) % n];
            const auto &p2 = points[(i + 2) % n];

            long long cross = crossProduct(p0, p1, p2);
            if (cross == 0) continue;   // collinear

            long long sign = (cross > 0) ? 1 : -1;
            if (initialSign == 0) {
                initialSign = sign;
            } else if (initialSign != sign) {
                return false; // sign flip → non‑convex
            }
        }
        return true;
    }

private:
    long long crossProduct(const std::vector<int>& a,
                           const std::vector<int>& b,
                           const std::vector<int>& c) const {
        // (b - a) × (c - b)
        long long abx = b[0] - a[0];
        long long aby = b[1] - a[1];
        long long bcx = c[0] - b[0];
        long long bcy = c[1] - b[1];
        return abx * bcy - aby * bcx;
    }
};
```

---

## Blog Article – *“Convex Polygon: The Good, the Bad, and the Ugly”*

### Introduction

> **“If you can’t describe it in plain English, you don’t understand it.”** – Albert Einstein  
> When interviewers ask you to implement a convex‑polygon checker, they’re really testing your ability to **translate geometry into code**.  
> In this article we dissect the LeetCode problem 469, walk through three clean solutions (Java, Python, C++), and discuss the common pitfalls that can turn a neat algorithm into a job‑rejecting mess.  

**Keywords**: Leetcode 469, Convex Polygon, Java solution, Python solution, C++ solution, job interview, cross product, algorithm design, coding interview tips

---

### 1. The Good – Why the Cross‑Product Test is Elegant

- **Mathematical clarity**: The cross product of two edges gives a signed area. A consistent sign across all triples tells us the polygon winds in one direction—exactly what convexity means.  
- **Linear time**: `O(n)` is optimal; we only need to inspect each edge once.  
- **No extra space**: We maintain just a single sign flag.  
- **Language agnostic**: Works in any language that can handle integer arithmetic.

**Takeaway for recruiters**: Candidates who recognize the cross‑product pattern show deep understanding of geometry and are ready to apply mathematical insight to real‑world problems.

---

### 2. The Bad – Common Mistakes That Kill Your Score

| Mistake | What Happens | Fix |
|---------|--------------|-----|
| Using `int` for cross product in Java/C++ | Integer overflow for coordinates near `10^4` | Use `long` (`int64_t`) |
| Ignoring collinear edges incorrectly | Returning `false` on a degenerate convex polygon | Skip `cross == 0` or treat it as neutral |
| Forgetting the wrap‑around indices | Crash on last vertex or wrong logic | Use modulo (`(i+1)%n`) |
| Assuming `n < 3` is valid | LeetCode gives 3 ≤ n, but a defensive check is harmless | Add guard, but it won’t hurt |
| Misreading “convex” as “strictly convex” | Failing on polygons with 180° angles | Decide on definition based on problem statement |

**Pro Tip**: Always write a small helper (`crossProduct`) and keep the main loop short. It reduces bugs and improves readability—something interviewers love to see.

---

### 3. The Ugly – Edge Cases That Trip Up Even Smart Solutions

| Edge Case | Why It’s Ugly | How to Handle |
|-----------|---------------|---------------|
| **All points collinear** | Technically not a polygon, but the algorithm may return `true` | Explicitly check if all cross products are zero; you can return `false` or `true` depending on the spec |
| **Large coordinates** | Overflow in 32‑bit environments | Promote to 64‑bit (Java `long`, C++ `long long`) |
| **Self‑intersecting input** | Problem guarantees simple polygon, but defensive code helps | Optionally run a simple O(n²) intersection test before proceeding |
| **Reversed point order** | Cross product sign flips but polygon remains convex | Our algorithm handles both clockwise and counter‑clockwise because we only check for consistency, not a fixed sign |

In interviews, interviewers sometimes inject trick inputs to see how you adapt. A solid solution will be robust, with clear comments explaining each design choice.

---

### 4. Full Code Review – How We Turn Good into Great

- **Java**: Uses `List<List<Integer>>`, `long` for safety, and a compact helper.  
- **Python**: Leverages type hints, functional style, and `int`’s arbitrary precision.  
- **C++**: Uses `vector<vector<int>>`, `long long`, and a private helper for clarity.

All three codes:

- Are **O(n)** in time, **O(1)** in extra space.  
- Contain **explanatory comments** (ideal for code reviews).  
- Use **modular arithmetic** to wrap around the polygon, preventing out‑of‑bounds errors.

---

### 5. Interview‑Ready Checklist

1. **Clarify the definition**: Ask if a polygon with 180° angles is considered convex.  
2. **Ask about constraints**: Confirm coordinate bounds to decide on data types.  
3. **Sketch the idea**: Talk through the cross‑product sign logic before coding.  
4. **Handle edge cases**: Mention how you treat collinear edges and degenerate polygons.  
5. **Explain time/space**: Show you can analyze complexity on the spot.  
6. **Write clean, testable code**: Use helper functions, type hints, and clear variable names.  

---

### Conclusion

The convex‑polygon checker is a classic LeetCode problem that blends geometry with algorithmic rigor. By focusing on the **cross‑product sign** we achieve an optimal, language‑agnostic solution that withstands most edge cases.  

**Good**: Simple, linear, mathematically solid.  
**Bad**: Overflow, collinear confusion, indexing errors.  
**Ugly**: Large coordinates, degenerate inputs, interview twists.

Armed with the code snippets above and the best‑practice checklist, you’re ready to ace the question, impress your interviewers, and land that job. Happy coding!  

---  

**Further reading**

- [LeetCode 469 – Convex Polygon](https://leetcode.com/problems/convex-polygon/description/)  
- [Cross Product – Geometric Interpretation](https://en.wikipedia.org/wiki/Cross_product)  
- [Interview Coding Patterns – Geometry](https://www.interviewbit.com/coding-interview-patterns/geometry/)  

Feel free to adapt the code to your preferred style and add unit tests to showcase robustness. Good luck!