---
title: LeetCode 391. Perfect Rectangle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – 391. Perfect Rectangle

**LeetCode**: https://leetcode.com/problems/perfect-rectangle/

You are given a list of *axis‑aligned* rectangles.  
Each rectangle is represented by its lower‑left corner `(x₁, y₁)` and upper‑right corner `(x₂, y₂)`.

> **Goal** – Determine whether the given set of rectangles together forms **exactly** one big rectangle with no gaps and no overlaps.

---

## 2.  The “Good, The Bad, The Ugly” of the Classic Set‑Based Solution

| Aspect | What to Love | Common Pitfalls | How to Avoid Them |
|--------|--------------|-----------------|-------------------|
| **Good** | • O(n) time, O(n) space. <br>• Handles the *“perfect rectangle”* definition in a single sweep. <br>• Works for 20 k rectangles (the problem limit). | **Bad** | • Mis‑handling of duplicate corner points (remove‑if‑present logic). <br>• Forgetting to compute the bounding rectangle’s area. <br>• Using a `Map<Point, Integer>` instead of a `Set` and ending up with a counter of “how many times a corner appears”. | **Ugly** | • Using a `HashSet` of stringified points can be slow for huge inputs. <br>• Trying to store all tiny sub‑rectangles to later compare shapes. <br>• Over‑engineering with segment trees or sweep‑lines when a set is enough. |

---

## 3.  Algorithm Overview

1. **Bounding Rectangle**  
   Track `minX`, `minY`, `maxX`, `maxY` while iterating over all rectangles.  
   The area of this bounding rectangle will be our *expected* total area.

2. **Area Accumulation**  
   For each rectangle compute its area `(x₂‑x₁) * (y₂‑y₁)` and add it to a running sum.

3. **Corner Set**  
   For every rectangle, insert its four corners into a `Set<Point>` (or a `HashSet<String>` for simplicity).  
   *If the point already exists, remove it – this is the “remove‑if‑present” trick.*  
   After processing all rectangles, only the **four corners of the bounding rectangle** should remain in the set.

4. **Final Checks**  
   * The set must contain exactly 4 points.  
   * Those four points must be the corners of the bounding rectangle.  
   * The summed area must equal the area of the bounding rectangle.  
   If all three conditions hold → **true**; otherwise → **false**.

This method is sometimes called the “corner‑counting” or “set‑based” approach.

---

## 4.  Reference Implementations

> **Tip** – All three snippets use the same logic; swap the language of choice in your interview or on a coding platform.

### 4.1  Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    private static class Point {
        final int x, y;
        Point(int x, int y) { this.x = x; this.y = y; }

        @Override public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Point)) return false;
            Point p = (Point) o;
            return x == p.x && y == p.y;
        }

        @Override public int hashCode() {
            return 31 * x + y;
        }
    }

    public boolean isRectangleCover(int[][] rectangles) {
        long areaSum = 0;
        int minX = Integer.MAX_VALUE, minY = Integer.MAX_VALUE;
        int maxX = Integer.MIN_VALUE, maxY = Integer.MIN_VALUE;

        Set<Point> cornerSet = new HashSet<>();

        for (int[] rect : rectangles) {
            int x1 = rect[0], y1 = rect[1];
            int x2 = rect[2], y2 = rect[3];

            // 1. Update bounding rectangle
            minX = Math.min(minX, x1);
            minY = Math.min(minY, y1);
            maxX = Math.max(maxX, x2);
            maxY = Math.max(maxY, y2);

            // 2. Sum area
            long rectArea = (long) (x2 - x1) * (y2 - y1);
            areaSum += rectArea;

            // 3. Manage corner set
            Point[] corners = {
                new Point(x1, y1),
                new Point(x1, y2),
                new Point(x2, y1),
                new Point(x2, y2)
            };
            for (Point p : corners) {
                if (!cornerSet.add(p)) { // already present → remove
                    cornerSet.remove(p);
                }
            }
        }

        // Expected area of bounding rectangle
        long expectedArea = (long) (maxX - minX) * (maxY - minY);
        if (areaSum != expectedArea) return false;

        // Corner set must contain exactly the 4 corners of the bounding rectangle
        if (cornerSet.size() != 4) return false;
        cornerSet.remove(new Point(minX, minY));
        cornerSet.remove(new Point(minX, maxY));
        cornerSet.remove(new Point(maxX, minY));
        cornerSet.remove(new Point(maxX, maxY));
        return cornerSet.isEmpty();
    }
}
```

### 4.2  Python

```python
from typing import List, Set, Tuple

class Solution:
    def isRectangleCover(self, rectangles: List[List[int]]) -> bool:
        min_x = min_y = float('inf')
        max_x = max_y = float('-inf')
        area = 0
        corners: Set[Tuple[int, int]] = set()

        for x1, y1, x2, y2 in rectangles:
            # Bounding rectangle
            min_x, min_y = min(min_x, x1), min(min_y, y1)
            max_x, max_y = max(max_x, x2), max(max_y, y2)

            # Area
            area += (x2 - x1) * (y2 - y1)

            # Corner handling
            pts = {(x1, y1), (x1, y2), (x2, y1), (x2, y2)}
            for p in pts:
                if p in corners:
                    corners.remove(p)
                else:
                    corners.add(p)

        # Check area
        if area != (max_x - min_x) * (max_y - min_y):
            return False

        # Check corner set
        expected = {(min_x, min_y), (min_x, max_y),
                    (max_x, min_y), (max_x, max_y)}
        return corners == expected
```

### 4.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isRectangleCover(vector<vector<int>>& rectangles) {
        long long area = 0;
        int minX = INT_MAX, minY = INT_MAX;
        int maxX = INT_MIN, maxY = INT_MIN;
        set<pair<int,int>> corners;

        for (auto &r : rectangles) {
            int x1 = r[0], y1 = r[1];
            int x2 = r[2], y2 = r[3];

            minX = min(minX, x1);
            minY = min(minY, y1);
            maxX = max(maxX, x2);
            maxY = max(maxY, y2);

            area += 1LL * (x2 - x1) * (y2 - y1);

            pair<int,int> c[4] = { {x1,y1}, {x1,y2}, {x2,y1}, {x2,y2} };
            for (auto &p : c) {
                if (!corners.insert(p).second) {
                    corners.erase(p);   // remove duplicate
                }
            }
        }

        long long expected = 1LL * (maxX - minX) * (maxY - minY);
        if (area != expected) return false;

        // After all operations, only the 4 outer corners should remain
        set<pair<int,int>> expectedCorners = {
            {minX, minY}, {minX, maxY},
            {maxX, minY}, {maxX, maxY}
        };
        return corners == expectedCorners;
    }
};
```

---

## 5.  Edge‑Case Checklist

| Case | Why it matters | How the algorithm handles it |
|------|----------------|------------------------------|
| **Zero‑area rectangle** | One side has zero length → not a valid rectangle. Problem guarantees `xi < ai` and `yi < bi`, so no need to check. |
| **Large coordinates** | Area can exceed 32‑bit; use 64‑bit (`long`/`long long`). |
| **Overlap** | Duplicate corners get removed; the area sum will exceed the bounding area. |
| **Gap** | Area sum will be less than the bounding area; corner set will have more than 4 points. |
| **All rectangles form one bigger rectangle** | Works: corners left are exactly the four outer corners, area matches. |

---

## 6.  Complexity Analysis

- **Time**: `O(n)` – one pass through the rectangles.
- **Space**: `O(n)` – the corner set can grow up to `4n` in the worst case before collapsing.

These bounds comfortably satisfy the constraints (`n ≤ 20,000`).

---

## 7.  “What If” – Alternative Approaches

| Approach | Pros | Cons |
|----------|------|------|
| **Sweep‑Line / Segment Tree** | Good for problems requiring dynamic range queries. | Overkill for this static problem; higher code complexity and memory usage. |
| **Prefix Sum on Grid** | Straightforward if coordinates are small. | Coordinates up to `10^5` → impossible memory/time. |
| **Hash Map of Points with Counts** | Works; same logic as set but counts duplicates. | Extra space for counts, but not necessary. |

The set‑based method wins in simplicity and performance.

---

## 8.  How to Use This on Your Resume / Interview

1. **Problem Statement** – Short but clear: “Verified whether a collection of axis‑aligned rectangles perfectly covers a larger rectangle without overlaps or gaps.”
2. **Solution** – “Implemented an O(n) corner‑set algorithm using hash‑sets to detect duplicate corners, computed area consistency, and validated bounding rectangle corners.”
3. **Tech Stack** – Highlight languages: Java, Python, C++.
4. **Key Takeaway** – “Demonstrated deep understanding of computational geometry, hash‑based deduplication, and algorithmic efficiency.”

---

## 9.  SEO‑Optimized Blog Post Draft

> **Title**  
> *Perfect Rectangle – A Deep Dive into LeetCode 391 (Java, Python & C++) – The Good, The Bad, and The Ugly*

> **Meta Description**  
> Master LeetCode’s Hard problem “Perfect Rectangle” with a clear, step‑by‑step solution in Java, Python, and C++. Learn the corner‑set trick, handle edge cases, and impress recruiters.

---

### Blog Outline

1. **Intro** – Why this problem is a staple in software‑engineering interviews.
2. **Problem Recap** – Quick definition, examples, constraints.
3. **Algorithm Overview** – Corner set, area sum, bounding rectangle.
4. **Why It Works** – Mathematical justification: no overlap → corners cancel out.
5. **Full Code Walk‑Through** – Java → Python → C++.
6. **Edge‑Case Exploration** – Overlaps, gaps, huge coordinates.
7. **Performance Profile** – `O(n)` time, `O(n)` memory; memory‑friendly even for 20 k rectangles.
8. **What Recruiters Look For** – Handling of big integers, set usage, clean code.
9. **Common Mistakes** – Forgetting to remove corners, using wrong data types.
10. **Conclusion** – Summary, how to adapt the pattern to other problems.

---

### Sample Intro Paragraph (SEO Friendly)

> “If you’re preparing for a technical interview, the **Perfect Rectangle** problem (LeetCode 391) is a must‑know. It tests your ability to reason about geometry, hash‑based deduplication, and big‑integer arithmetic—all skills highly prized by top tech firms. In this article, we’ll walk through the optimal `O(n)` solution, provide ready‑to‑copy implementations in **Java, Python, and C++**, and dissect why this trick is both elegant and powerful. Whether you’re a front‑end developer, a data‑science enthusiast, or a backend engineer, mastering this pattern will give you an edge in your next interview.”

---

### Closing Call‑to‑Action

> “Ready to ace your next coding challenge? Download the three‑language solution, run it against your own test cases, and share your results on GitHub. Tag the post with **#LeetCode391** and let recruiters see your growth.”

---

## 10.  Final Takeaway

The corner‑set algorithm is a canonical example of how **hash tables can solve geometric problems efficiently**. With clean code in Java, Python, and C++, you have concrete artifacts to showcase during interviews and on your résumé. Keep the edge‑case checklist handy, remember to use 64‑bit arithmetic, and you’ll turn LeetCode 391 from a hard hurdle into a brag‑worthy triumph. Happy coding! 🚀
