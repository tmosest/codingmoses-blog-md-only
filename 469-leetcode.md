---
title: LeetCode 469. Convex Polygon - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Solution (Java / Python / C++)

Below you’ll find three complete, ready‑to‑compile solutions – one in **Java**, one in **Python**, and one in **C++** – that solve LeetCode problem **469 – Convex Polygon**.  
All three use the same O(n) algorithm based on the sign of the cross product of consecutive edges.

> **Why the cross product?**  
> For a simple polygon, every turn must be in the same rotational direction (all left or all right) for the shape to be convex.  
> The sign of the cross product `((x2−x1)*(y3−y2) − (y2−y1)*(x3−x2))` tells us whether we are turning left (`>0`), right (`<0`), or are collinear (`=0`).  
> If the sign ever changes (ignoring zeroes), the polygon is **not** convex.

--------------------------------------------------------------------

### Java

```java
import java.util.List;

public class Solution {
    public boolean isConvex(List<List<Integer>> points) {
        int n = points.size();
        if (n < 3) return false; // just for safety

        // Determine the sign of the first non‑zero cross product
        long sign = 0;
        for (int i = 0; i < n; i++) {
            long cross = crossProduct(
                    points.get(i),
                    points.get((i + 1) % n),
                    points.get((i + 2) % n));
            if (cross != 0) {
                sign = cross > 0 ? 1 : -1;
                break;
            }
        }
        // If all cross products are zero, polygon is degenerate but still considered convex
        if (sign == 0) return true;

        // Verify that all cross products have the same sign
        for (int i = 0; i < n; i++) {
            long cross = crossProduct(
                    points.get(i),
                    points.get((i + 1) % n),
                    points.get((i + 2) % n));
            if (cross != 0) {
                long currentSign = cross > 0 ? 1 : -1;
                if (currentSign != sign) return false;
            }
        }
        return true;
    }

    private long crossProduct(List<Integer> a, List<Integer> b, List<Integer> c) {
        long x1 = b.get(0) - a.get(0);
        long y1 = b.get(1) - a.get(1);
        long x2 = c.get(0) - b.get(0);
        long y2 = c.get(1) - b.get(1);
        return x1 * y2 - y1 * x2;
    }
}
```

--------------------------------------------------------------------

### Python

```python
from typing import List

class Solution:
    def isConvex(self, points: List[List[int]]) -> bool:
        n = len(points)
        if n < 3:
            return False

        # Find the first non‑zero cross product sign
        sign = 0
        for i in range(n):
            cross = self._cross(points[i], points[(i + 1) % n], points[(i + 2) % n])
            if cross:
                sign = 1 if cross > 0 else -1
                break

        # Degenerate (all collinear) polygons are considered convex
        if sign == 0:
            return True

        # Check the rest of the edges
        for i in range(n):
            cross = self._cross(points[i], points[(i + 1) % n], points[(i + 2) % n])
            if cross and (1 if cross > 0 else -1) != sign:
                return False
        return True

    @staticmethod
    def _cross(a, b, c):
        x1, y1 = b[0] - a[0], b[1] - a[1]
        x2, y2 = c[0] - b[0], c[1] - b[1]
        return x1 * y2 - y1 * x2
```

--------------------------------------------------------------------

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool isConvex(vector<vector<int>>& points) {
        int n = points.size();
        if (n < 3) return false;               // safety guard

        long long sign = 0;                     // 0 = unknown, 1 = left, -1 = right
        for (int i = 0; i < n; ++i) {
            long long cross = cross(points[i], points[(i + 1) % n], points[(i + 2) % n]);
            if (cross != 0) {
                sign = (cross > 0) ? 1 : -1;
                break;
            }
        }

        if (sign == 0) return true;            // all points collinear

        for (int i = 0; i < n; ++i) {
            long long cross = cross(points[i], points[(i + 1) % n], points[(i + 2) % n]);
            if (cross != 0) {
                long long curr = (cross > 0) ? 1 : -1;
                if (curr != sign) return false;
            }
        }
        return true;
    }

private:
    long long cross(const vector<int>& a, const vector<int>& b, const vector<int>& c) {
        long long x1 = b[0] - a[0], y1 = b[1] - a[1];
        long long x2 = c[0] - b[0], y2 = c[1] - b[1];
        return x1 * y2 - y1 * x2;
    }
};
```

--------------------------------------------------------------------

## 2.  Blog Article – “Convex Polygon, the Good, the Bad, and the Ugly”

### Meta Description
> Master LeetCode 469 – Convex Polygon. Dive into the clean O(n) cross‑product algorithm, learn pitfalls, and see Java, Python, and C++ implementations. Boost your interview score and land that job.

---

### Introduction

“Convex Polygon” – a name that feels deceptively simple, but that’s exactly why it trips up many algorithmists. On LeetCode, problem 469 asks you to decide whether a given simple polygon is convex.  

**Why does this matter?**  
In real‑world applications, convexity guarantees that the shape is “well‑behaved”: any line segment connecting two points inside the shape stays inside, which is essential for collision detection, rendering, and computational geometry. In interviews, it tests your understanding of vectors, cross products, and edge‑case handling.

Below, we dissect the problem, walk through the most efficient solution, examine what goes wrong (the *bad*), and then show how to avoid common traps (the *ugly*). Finally, we provide ready‑to‑copy code in **Java, Python, and C++** – all you need to impress hiring managers.

---

### 1. Problem Recap

You’re given an ordered list of points that form a simple polygon (no self‑intersections).  
Return **true** if the polygon is convex, otherwise **false**.

- `3 <= points.length <= 10^4`
- All points are unique.
- Polygon is guaranteed to be simple.

**Example**

```text
Input: [[0,0],[0,5],[5,5],[5,0]]   ->  true
Input: [[0,0],[0,10],[10,10],[10,0],[5,5]] -> false
```

---

### 2. The “Good” – A clean O(n) algorithm

#### 2.1 Idea: Cross product sign consistency

For consecutive edges `AB` and `BC`:

```
cross(AB, BC) = (Bx-Ax)*(Cy-By) - (By-Ay)*(Cx-Bx)
```

- `cross > 0`  → Left turn  
- `cross < 0`  → Right turn  
- `cross == 0` → Collinear

A convex polygon must turn in **the same direction** at every vertex (ignoring collinear points).  

#### 2.2 Algorithm

```
1. n = points.length
2. Find the first non‑zero cross product; record its sign as 'direction'.
   If all cross products are zero → degenerate polygon, treat as convex.
3. Iterate over all vertices:
     compute cross(AB, BC)
     if cross != 0 and sign(cross) != direction → return false
4. Return true
```

All operations are O(1); the loop runs `n` times → **O(n)** time, **O(1)** space.

#### 2.3 Why it works

Because the polygon is simple, any change in turning direction must introduce an “indentation” (concave vertex). By verifying sign consistency across the cycle, we detect precisely those indentations.

---

### 3. The “Bad” – Common pitfalls

| Pitfall | Why it fails | Example |
|---------|--------------|---------|
| **Using floating point** | Precision loss when coordinates are large (up to 10^4) | `cross` computed with doubles may flip sign for near‑collinear points. |
| **Ignoring modulo wrap‑around** | Last vertex not connected to first | Using `i+2` without `% n` will access out‑of‑bounds indices. |
| **Early return on zero cross** | Treating collinear triples as immediately convex | A collinear edge followed by a concave turn should still be flagged. |
| **Assuming all points are convex** | Overlooking degenerate polygons | A line segment (`n=3`, all points collinear) should return true. |

---

### 4. The “Ugly” – Hidden edge cases

1. **All points collinear**  
   – Cross products are zero. Some solutions incorrectly return *false*.  
   – Solution: If the first non‑zero sign is never found, return *true*.

2. **Polygon with 3 points**  
   – Always convex, but ensure the algorithm doesn’t divide by zero.

3. **Large coordinates**  
   – Use 64‑bit integers (`long` in Java/C++, `int` in Python 3 which is arbitrary‑precision) to avoid overflow in cross product.

4. **Negative coordinates**  
   – Works fine; cross product sign handles both positive and negative values.

---

### 5. Code snippets (Java / Python / C++)

(See Section 1 for full implementations.)

---

### 6. Testing Strategy

| Test | Description | Expected |
|------|-------------|----------|
| `[[0,0],[1,0],[1,1],[0,1]]` | Simple square | `true` |
| `[[0,0],[1,0],[0,1]]` | Right‑angled triangle | `true` |
| `[[0,0],[2,0],[1,1],[2,2],[0,2]]` | Concave shape | `false` |
| `[[0,0],[1,0],[2,0],[3,0]]` | All collinear | `true` |
| Random 10k points on a convex hull | Stress test | `true` |

Automated tests with random permutations of convex hull points and deliberate insertion of a concave vertex ensure robustness.

---

### 7. Take‑away for Interviewers

- **Ask about the cross product**: If the candidate knows to use it, they’re likely comfortable with 2‑D geometry.
- **Edge‑case handling**: Test whether they consider collinear points, wrap‑around indexing, and integer overflow.
- **Time & space complexity**: Expect an O(n) solution with O(1) auxiliary memory.

---

### 8. SEO & Job‑Interview Tips

| Keyword | Placement | Why |
|---------|-----------|-----|
| `LeetCode 469` | Title, first paragraph | Exact match query. |
| `convex polygon algorithm` | Headings, code comments | High intent. |
| `cross product convex polygon` | Content, FAQs | Technical depth. |
| `job interview coding questions` | Meta description, conclusion | Broad audience. |
| `Java Python C++ solutions` | Code sections | Languages requested. |

*Tip:* In your résumé or portfolio, link to a blog post that includes these code snippets. Hiring managers love concrete, copy‑pasteable solutions.

---

### 9. Final Thoughts

Convexity is a classic problem that bridges geometry and algorithm design.  
The **cross‑product sign consistency** method is not only optimal but also elegant—few lines of code, clear reasoning, and no hidden pitfalls when implemented carefully.  

Drop the code, practice the test cases, and you’ll walk into your next interview with confidence. Good luck, and may your convex polygons always stay true!