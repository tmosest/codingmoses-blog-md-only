---
title: LeetCode 1828. Queries on Number of Points Inside a Circle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1828 – “Queries on Number of Points Inside a Circle”
> **Goal:** Count how many of the given points lie inside (or on the border of) each query circle.  
> **Platforms:** Java, Python, C++  
> **SEO Keywords:** LeetCode 1828, Queries on Number of Points Inside a Circle, Java solution, Python solution, C++ solution, algorithm interview, O(n²) time, job interview tips

---

### 1. 📌 Problem Statement (Simplified)

You are given:

| Variable | Meaning |
|----------|---------|
| `points[i] = [xi, yi]` | Coordinates of the *i*-th point (0 ≤ xi, yi ≤ 500) |
| `queries[j] = [xj, yj, rj]` | Center `(xj, yj)` and radius `rj` (1 ≤ rj ≤ 500) |

For each query, output the number of points that satisfy

```
(xi – xj)² + (yi – yj)²  ≤  rj²
```

(Points on the border count as inside.)

Constraints:  
`1 ≤ points.length, queries.length ≤ 500`

---

### 2. ⚙️ Naïve Brute‑Force Approach

1. **Loop over every query.**  
2. **Loop over every point.**  
3. Compute squared distance and compare with `r²`.  

> **Time Complexity:** `O(Q · P)` → at most `500 × 500 = 250,000` distance checks.  
> **Space Complexity:** `O(1)` (besides the result array).

With the given limits, this solution passes comfortably, but we’ll also explore a faster method.

---

### 3. 🧠 “Good, Bad, Ugly” of the Brute‑Force

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Implementation** | Simple, one‑pass loops | Requires `Math.pow` or manual multiplication | Over‑using floating point math (`Math.pow`) can be slow and imprecise |
| **Performance** | Acceptable for 500×500 | Quadratic → may become heavy for larger constraints | O(n²) can hit time limits on more demanding tests |
| **Readability** | Clear, few lines | Repetitive code | Hard to tweak for edge‑cases (e.g., overflow if using int on big coordinates) |
| **Maintainability** | Easy to understand | Doesn’t scale | Hard to modify for new features (e.g., weighted points) |

---

### 4. ⚡️ Optimizing with a 2‑D Prefix Sum (Grid)

Because all coordinates lie in `[0, 500]`, we can pre‑build a 2‑D grid `cnt[x][y]` that records how many points sit at each cell. Then we compute a prefix sum `pre[x][y]`. For any circle we can:

1. **Iterate over x** from `cx - r` to `cx + r`.  
2. For each `x`, compute the `y`‑range that satisfies the circle inequality.  
3. Use the prefix sum to query how many points lie between the two y‑boundaries in *O(1)*.  

This reduces each query from `O(P)` to `O(r)` (≤ 500). Overall complexity: `O(P + Q·maxR)` → still good for the given limits.

---

### 5. 📚 Implementations

Below are clean, production‑ready solutions for **Java, Python, and C++**.  
All three use the brute‑force method for clarity; the prefix‑sum variant is also shown as a comment for advanced readers.

---

#### 5.1 Java (Java 17)

```java
// File: Solution.java
import java.util.*;

public class Solution {
    public int[] countPoints(int[][] points, int[][] queries) {
        int qLen = queries.length;
        int[] result = new int[qLen];

        for (int i = 0; i < qLen; ++i) {
            int cx = queries[i][0];
            int cy = queries[i][1];
            int r  = queries[i][2];
            long r2 = (long) r * r;          // use long to avoid overflow

            int cnt = 0;
            for (int[] p : points) {
                long dx = p[0] - cx;
                long dy = p[1] - cy;
                if (dx * dx + dy * dy <= r2) cnt++;
            }
            result[i] = cnt;
        }
        return result;
    }

    /* ---------- Optional Optimized Grid Version ----------
    // Pre‑compute a 2D prefix sum for O(r) query time.
    // Uncomment if you want to use it instead of the naive loop.
    public int[] countPointsGrid(int[][] points, int[][] queries) {
        final int MAX = 500;
        int[][] cnt = new int[MAX + 1][MAX + 1];
        for (int[] p : points) cnt[p[0]][p[1]]++;

        // Build prefix sums
        int[][] pre = new int[MAX + 2][MAX + 2];
        for (int x = 0; x <= MAX; ++x) {
            for (int y = 0; y <= MAX; ++y) {
                pre[x + 1][y + 1] = cnt[x][y] + pre[x][y + 1]
                        + pre[x + 1][y] - pre[x][y];
            }
        }

        int qLen = queries.length;
        int[] res = new int[qLen];
        for (int i = 0; i < qLen; ++i) {
            int cx = queries[i][0];
            int cy = queries[i][1];
            int r  = queries[i][2];
            int minX = Math.max(0, cx - r);
            int maxX = Math.min(MAX, cx + r);
            int total = 0;

            for (int x = minX; x <= maxX; ++x) {
                int dx = x - cx;
                int maxDy = (int) Math.floor(Math.sqrt(r * r - dx * dx));
                int minY = Math.max(0, cy - maxDy);
                int maxY = Math.min(MAX, cy + maxDy);
                total += queryPrefix(pre, minX, minY, maxX, maxY);
            }
            res[i] = total;
        }
        return res;
    }

    private int queryPrefix(int[][] pre, int minX, int minY, int maxX, int maxY) {
        return pre[maxX + 1][maxY + 1] - pre[minX][maxY + 1]
             - pre[maxX + 1][minY] + pre[minX][minY];
    }
    ----------------------------------- */
}
```

**Why this is job‑ready**

- Uses `long` to avoid integer overflow.  
- Avoids `Math.pow` for speed.  
- Clear variable names (`cx`, `cy`, `r2`).  
- Self‑contained, no external dependencies.

---

#### 5.2 Python 3

```python
# File: solution.py
from typing import List

class Solution:
    def countPoints(self, points: List[List[int]],
                    queries: List[List[int]]) -> List[int]:
        res = []
        for cx, cy, r in queries:
            r2 = r * r
            cnt = 0
            for x, y in points:
                dx = x - cx
                dy = y - cy
                if dx * dx + dy * dy <= r2:
                    cnt += 1
            res.append(cnt)
        return res

# ---------------------------------------------------------
# Optional grid‑based optimization (prefix sums)
# ---------------------------------------------------------
# def countPointsGrid(points, queries):
#     MAX = 500
#     cnt = [[0]*(MAX+1) for _ in range(MAX+1)]
#     for x, y in points:
#         cnt[x][y] += 1
#     pre = [[0]*(MAX+2) for _ in range(MAX+2)]
#     for x in range(MAX+1):
#         for y in range(MAX+1):
#             pre[x+1][y+1] = cnt[x][y] + pre[x][y+1] + pre[x+1][y] - pre[x][y]
#     def query(minx, miny, maxx, maxy):
#         return pre[maxx+1][maxy+1] - pre[minx][maxy+1] - pre[maxx+1][miny] + pre[minx][miny]
#     res = []
#     for cx, cy, r in queries:
#         total = 0
#         for x in range(max(0, cx-r), min(MAX, cx+r)+1):
#             dx = x - cx
#             max_dy = int((r*r - dx*dx)**0.5)
#             miny = max(0, cy-max_dy)
#             maxy = min(MAX, cy+max_dy)
#             total += query(x, miny, x, maxy)
#         res.append(total)
#     return res
```

**Python‑friendly highlights**

- Explicit typing keeps the code type‑safe for static analyzers.  
- Uses plain integer arithmetic for speed.  
- The optional grid version is commented out but ready for copy‑paste.

---

#### 5.3 C++17

```cpp
// File: solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> countPoints(vector<vector<int>>& points,
                            vector<vector<int>>& queries) {
        vector<int> ans;
        for (auto& q : queries) {
            int cx = q[0], cy = q[1], r = q[2];
            long long r2 = 1LL * r * r;          // 64‑bit guard
            int cnt = 0;
            for (auto& p : points) {
                long long dx = p[0] - cx;
                long long dy = p[1] - cy;
                if (dx * dx + dy * dy <= r2) ++cnt;
            }
            ans.push_back(cnt);
        }
        return ans;
    }

    /* -----------------------------------------------------
       // Optional optimized grid (prefix sum) implementation
       -----------------------------------------------------
    vector<int> countPointsGrid(vector<vector<int>>& points,
                                vector<vector<int>>& queries) {
        const int MAX = 500;
        vector<vector<int>> cnt(MAX+1, vector<int>(MAX+1, 0));
        for (auto& p : points) cnt[p[0]][p[1]]++;

        // 2‑D prefix sums
        vector<vector<int>> pre(MAX+2, vector<int>(MAX+2, 0));
        for (int x=0; x<=MAX; ++x)
            for (int y=0; y<=MAX; ++y)
                pre[x+1][y+1] = cnt[x][y] + pre[x][y+1] + pre[x+1][y] - pre[x][y];

        auto query = [&](int minx, int miny, int maxx, int maxy) {
            return pre[maxx+1][maxy+1] - pre[minx][maxy+1]
                 - pre[maxx+1][miny] + pre[minx][miny];
        };

        vector<int> res;
        for (auto& q : queries) {
            int cx = q[0], cy = q[1], r = q[2];
            int total = 0;
            for (int x = max(0, cx-r); x <= min(MAX, cx+r); ++x) {
                long long dx = x - cx;
                int maxdy = static_cast<int>(sqrt(r*r - dx*dx));
                int miny = max(0, cy-maxdy);
                int maxy = min(MAX, cy+maxdy);
                total += query(minx=min(0, cx-r), miny, maxx=max(0, cx+r), maxy);
            }
            res.push_back(total);
        }
        return res;
    }
    ----------------------------------------------------- */
};
```

**Key job‑ready traits**

- Avoids `pow()` by using integer multiplication.  
- `long long` guarantees safety against overflow.  
- Standard `<bits/stdc++.h>` header keeps the file minimal.

---

### 6. 🔎 Complexity Recap

| Implementation | `T(Q,P)` | `S` |
|----------------|----------|-----|
| Brute‑force | `O(Q · P)` (≤ 2.5 × 10⁵ ops) | `O(1)` |
| Grid‑prefix (optional) | `O(P + Q·maxR)` (≤ 2.5 × 10⁵ + 500·500) | `O(1)` plus `O(MAX²)` grid memory (≈ 250k ints) |

Both approaches are **well within the 1‑second limit** on LeetCode. The grid variant shines when the constraints grow (e.g., 10,000 points or 10,000 queries).

---

### 7. 🧪 Sample Test Case

```text
points   = [[1, 1], [2, 2], [3, 3], [4, 4]]
queries  = [[2, 2, 2], [1, 1, 1], [0, 0, 10]]
```

| Query | Result |
|-------|--------|
| (2, 2, 2) | 3 (points at (1,1), (2,2), (3,3) are inside) |
| (1, 1, 1) | 1 (only (1,1)) |
| (0, 0, 10)| 4 (all points inside a big circle) |

Running any of the three implementations on this input returns `[3, 1, 4]`.

---

### 8. ⚠️ Edge‑Case Checklist

| Edge | What to Watch |
|------|---------------|
| **Large radius** (`r = 500`) | `dx² + dy²` can reach `2.5 × 10⁵` → fits in `int` but use `long`/`long long` to be safe. |
| **Zero points** | Result should be `[0, 0, …]`. |
| **Duplicate points** | Each duplicate contributes separately – the algorithm counts them all. |
| **All points on the border** | Should still be counted; inequality is `≤`. |
| **Overflow** | Use 64‑bit (`long`/`long long`) for distance calculation. |

---

### 9. 🏁 Final Thoughts

- **For interviewers**: The straightforward O(Q·P) solution is a solid baseline. It demonstrates mastery of basic loops, distance formulas, and careful type handling.  
- **For recruiters**: Your code should be **compact, correct, and safe**—the Java example above meets those criteria.  
- **For self‑learning**: Try swapping the naive loop for the grid‑prefix version. It teaches 2‑D prefix sums, which appear in many advanced algorithm questions (range queries, Manhattan distance, etc.).

---

### 10. 🎯 Take‑away for the Next Job Interview

1. **Start simple.** Show that you can solve the problem with clear logic before diving into optimizations.  
2. **Explain your trade‑offs.** Interviewers love when you discuss the “good, bad, ugly” of your solution.  
3. **Keep code clean:**  
   - Avoid unnecessary function calls (`Math.pow`).  
   - Use 64‑bit integers where overflow is a risk.  
   - Name variables descriptively.  
4. **Test rigorously**: Include corner cases in your unit tests.  
5. **Speak about scaling**: Mention how the solution could be improved if the constraints grew (grid prefix sums, spatial indexing, etc.).  

By following these steps you’ll not only ace LeetCode 1828 but also demonstrate the engineering mindset that recruiters look for.

Happy coding! 🎉

---