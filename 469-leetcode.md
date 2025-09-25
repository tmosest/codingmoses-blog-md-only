---
title: LeetCode 469. Convex Polygon - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  LeetCode 469 – Convex Polygon  
**Problem** – Check if a simple polygon (given by a list of its vertices in order) is convex.  
**Goal** – Return `true` if every interior angle is < 180° (colinear edges are allowed), otherwise `false`.

---

### 2️⃣  Algorithm Overview  

| Step | Action | Why it works |
|------|--------|--------------|
| 1 | Compute the **cross product** for every consecutive triple of points \((p_i, p_{i+1}, p_{i+2})\) (indices wrap around). | The sign of the cross product tells us whether the turn from \(p_i\to p_{i+1}\) to \(p_{i+1}\to p_{i+2}\) is **left** (`>0`) or **right** (`<0`). |
| 2 | Store the first non‑zero cross sign as the *reference orientation*. | A convex polygon must turn in the same direction at every vertex. |
| 3 | For every other non‑zero cross product, compare its sign to the reference. If they differ → **not convex**. | Any change of direction means an interior angle > 180°. |
| 4 | If all turns share the same sign (or are zero), the polygon is convex. | Zero turns represent colinear edges – still convex. |

**Complexity** – \(O(n)\) time, \(O(1)\) extra space.  
**Precision** – Use `long` for the cross product to avoid overflow (max \(|x|,|y| ≤ 10^4\) → product ≤ \(4×10^8\), still fits in 32‑bit, but `long` is safe).

---

### 3️⃣  Code Implementations  

Below are ready‑to‑run solutions in **Java**, **Python**, and **C++**.

---

#### 3.1 Java

```java
import java.util.List;

public class Solution {
    public boolean isConvex(List<List<Integer>> points) {
        int n = points.size();
        if (n < 3) return false;   // not a polygon, but constraints guarantee n >= 3

        int sign = 0;   // 0 = unknown, 1 = positive, -1 = negative
        for (int i = 0; i < n; i++) {
            List<Integer> p0 = points.get(i);
            List<Integer> p1 = points.get((i + 1) % n);
            List<Integer> p2 = points.get((i + 2) % n);

            long cross = crossProduct(p0, p1, p2);
            if (cross == 0) continue;          // colinear, ignore
            if (sign == 0) sign = cross > 0 ? 1 : -1;
            else if ((cross > 0 ? 1 : -1) != sign) {
                return false;                  // turn direction changed
            }
        }
        return true;
    }

    private long crossProduct(List<Integer> a, List<Integer> b, List<Integer> c) {
        long x1 = b.get(0) - a.get(0);
        long y1 = b.get(1) - a.get(1);
        long x2 = c.get(0) - a.get(0);
        long y2 = c.get(1) - a.get(1);
        return x1 * y2 - y1 * x2;
    }
}
```

---

#### 3.2 Python

```python
class Solution:
    def isConvex(self, points: list[list[int]]) -> bool:
        n = len(points)
        sign = 0  # 0 = unknown, 1 = positive, -1 = negative

        for i in range(n):
            p0 = points[i]
            p1 = points[(i + 1) % n]
            p2 = points[(i + 2) % n]

            cross = (p1[0] - p0[0]) * (p2[1] - p0[1]) - \
                    (p1[1] - p0[1]) * (p2[0] - p0[0])

            if cross == 0:
                continue
            if sign == 0:
                sign = 1 if cross > 0 else -1
            elif (1 if cross > 0 else -1) != sign:
                return False

        return True
```

---

#### 3.3 C++

```cpp
class Solution {
public:
    bool isConvex(vector<vector<int>>& points) {
        int n = points.size();
        int sign = 0;  // 0 = unknown, 1 = positive, -1 = negative

        for (int i = 0; i < n; ++i) {
            const auto& p0 = points[i];
            const auto& p1 = points[(i + 1) % n];
            const auto& p2 = points[(i + 2) % n];

            long long cross = 1LL * (p1[0] - p0[0]) * (p2[1] - p0[1]) -
                              1LL * (p1[1] - p0[1]) * (p2[0] - p0[0]);

            if (cross == 0) continue;          // colinear
            if (sign == 0) sign = (cross > 0) ? 1 : -1;
            else if ((cross > 0 ? 1 : -1) != sign) return false;
        }
        return true;
    }
};
```

---

### 4️⃣  Blog Post: “Convex Polygon – The Good, The Bad, and The Ugly”  

> **Keywords:** LeetCode 469, Convex Polygon, algorithm, Java solution, Python solution, C++ solution, software engineering interview, data structures, job interview, coding interview, algorithm design, convex polygon check, job search, resume optimization, SEO

---

#### 4.1 Introduction  

When you’re gearing up for a **software engineering interview**, you’ll encounter problems that test both *data structure knowledge* and *algorithmic intuition*. One classic challenge is **LeetCode 469 – Convex Polygon**. Though the statement looks simple—just check whether a polygon is convex—there are subtle pitfalls that can trip you up.  

In this post, we’ll walk through:

1.  The *good* – why a cross‑product based approach is elegant and fast.  
2.  The *bad* – common mistakes (overflow, colinear handling, orientation).  
3.  The *ugly* – edge‑case headaches and how to tame them.  

And we’ll sprinkle in some SEO‑friendly pointers to help your résumé stand out when recruiters search for “LeetCode convex polygon” or “algorithm interview solutions.”  

---

#### 4.2 The Good – Cross Product, Linear Time, O(1) Space  

A convex polygon is one where all turns from one edge to the next go the *same* way (either all left or all right). The 2‑D cross product gives you exactly that information:

```
cross((p2-p1), (p3-p2)) = (x2-x1)*(y3-y2) - (y2-y1)*(x3-x2)
```

*If the sign is positive → left turn; negative → right turn; zero → colinear.*

Because we only need to compare the sign of each cross product, the algorithm runs in **O(n)** time with **O(1)** extra memory – a perfect fit for interview settings where efficiency matters.

**Why cross product?**  
- It’s a *constant‑time* operation for each triple.  
- No need for sorting or complex geometric libraries.  
- Handles any coordinate range comfortably when we use 64‑bit integers.

---

#### 4.3 The Bad – Common Traps  

| Trap | What goes wrong | Fix |
|------|----------------|-----|
| **Integer overflow** | Using 32‑bit `int` can overflow if coordinates are near ±10⁴ (product up to 4×10⁸). | Use `long`/`long long`. |
| **Ignoring colinear edges** | Some people return *false* when a cross product is zero. | Zero is allowed for convexity (colinear edges). Treat it as “no change”. |
| **Wrong orientation handling** | Assuming the polygon is given in clockwise order. | Don’t rely on input order; compute first non‑zero sign as reference. |
| **Off‑by‑one errors** | Mis‑wrapping indices can skip a vertex or double‑count. | Use modulo arithmetic (`(i+1)%n`, `(i+2)%n`). |

---

#### 4.4 The Ugly – Edge Cases That Persist  

1. **All points colinear**  
   - A degenerate “polygon” with all points on a line technically is convex, but some solutions may incorrectly return *false*.  
   - *Solution*: If every cross product is zero, consider the shape convex (or handle as a special case if your job spec says otherwise).

2. **Duplicate vertices**  
   - LeetCode guarantees unique points, but in real‑world data you might encounter duplicates.  
   - *Solution*: Remove duplicates or flag as invalid early.

3. **Large coordinate values**  
   - Even with `long`, extremely large coordinates (beyond 32‑bit) could still overflow in a cross product.  
   - *Solution*: Use arbitrary‑precision arithmetic (`BigInteger` in Java) if needed, or normalize coordinates.

4. **Non‑simple polygons**  
   - The problem guarantees a simple polygon, but real data might contain self‑intersections.  
   - *Solution*: First run a segment‑intersection test (O(n²) or sweep line) before checking convexity.

---

#### 4.5 Interview Tips & SEO Boost  

1. **Show the math** – When explaining your solution, walk through the cross‑product derivation. Recruiters love seeing you connect geometry to code.  
2. **Edge‑case talk** – Mention the edge cases you considered (colinear, duplicates). It demonstrates thoroughness.  
3. **Mention time complexity** – Interviewers want to see you discuss \(O(n)\) vs. \(O(n \log n)\).  
4. **Provide multiple implementations** – Having Java, Python, and C++ solutions shows versatility.  
5. **SEO‑friendly résumé headline** – “LeetCode 469 Convex Polygon – Java, Python, C++ solutions, algorithmic design, interview-ready.”  
6. **Add tags** – When posting on platforms like GitHub or LeetCode, tag your solutions with `#convex-polygon #geometry #algorithm #leetcodelove`.

---

#### 4.6 Conclusion  

The **Convex Polygon** problem is a great showcase of *geometric insight* and *algorithmic simplicity*. By leveraging cross products, you can deliver an interview‑ready solution that is both fast and clear.  

Remember:  

- **Good** – O(n) time, constant space, intuitive cross product.  
- **Bad** – Beware overflow, colinear handling, orientation.  
- **Ugly** – Handle degenerate cases and non‑simple inputs.  

Now you’re ready to ace the LeetCode challenge, impress recruiters, and score that dream software engineering role! 🚀  

--- 

**Resources**  
- [LeetCode 469 – Convex Polygon](https://leetcode.com/problems/convex-polygon/)  
- Java solution: *cross-product by j9hv* (LeetCode)  
- Python & C++ equivalents included above.  

Happy coding!