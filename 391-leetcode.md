---
title: LeetCode 391. Perfect Rectangle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏆 391. Perfect Rectangle – LeetCode Hard  
**Goal:**  
Given a list of axis‑aligned rectangles, determine whether they together form **exactly one** big rectangle (no gaps, no overlaps).

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n)** | **O(n)** |
| Python | **O(n)** | **O(n)** |
| C++ | **O(n)** | **O(n)** |

---

## 📖 Blog Article – “The Good, the Bad, and the Ugly of Perfect Rectangle”  

> **Keywords:** Perfect Rectangle LeetCode, algorithm, Java solution, Python solution, C++ solution, interview question, hash set, area check, job interview.

### 1️⃣ What Is the Perfect Rectangle Problem?

Imagine you have a set of smaller rectangles placed on a 2‑D plane.  
You’re asked: **Do they cover a single larger rectangle without any holes or overlaps?**

Typical interview question that tests:
- **Geometric insight**  
- **Hashing / Set usage**  
- **Math & area computation**

### 2️⃣ The Classic O(n) Solution

The trick is to combine two simple observations:

| Observation | Why it matters |
|-------------|----------------|
| **Area check** | The sum of all small rectangles’ areas must equal the area of the outer bounding rectangle. |
| **Corner check** | In a perfect cover every internal corner appears *an even number of times*; only the 4 outer corners appear *exactly once*. |

#### Steps

1. **Track the outer bounds**  
   `minX`, `minY`, `maxX`, `maxY` – the smallest rectangle that could contain all others.

2. **Sum areas**  
   `totalArea = Σ (ai - xi) * (bi - yi)`

3. **Maintain a Set of corners**  
   For each rectangle push its 4 corners into a `HashSet`.  
   If a corner already exists, remove it – this effectively keeps only the *odd‑frequency* corners.

4. **After processing all rectangles**  
   * The set must contain **exactly four corners**.  
   * Those four corners must be `(minX, minY)`, `(minX, maxY)`, `(maxX, minY)`, `(maxX, maxY)`.  
   * The `totalArea` must equal `(maxX - minX) * (maxY - minY)`.

If all three conditions are satisfied → **true**; else **false**.

> **Why does this work?**  
> Any internal corner (shared by 2 or 4 rectangles) gets added and removed an even number of times → disappears from the set. Only the outer rectangle’s corners survive.

### 3️⃣ The Good

| ✅ Feature | Explanation |
|-----------|-------------|
| **Linear time** | Each rectangle is processed once. |
| **Low memory** | Only a set of at most 4 corners at the end; plus a few integers. |
| **Deterministic** | No randomness or recursion – great for interview clarity. |
| **Extensible** | Works for any axis‑aligned rectangles. |

### 4️⃣ The Bad

| ⚠️ Problem | Why it’s a pain |
|-----------|-----------------|
| **Large coordinates** | Need `long`/`int64` to avoid overflow when computing area. |
| **HashSet overhead** | In tight interview constraints, you might need a custom pair hash. |
| **Assumes proper input** | Doesn’t guard against degenerate rectangles (`xi==ai` or `yi==bi`). |

### 5️⃣ The Ugly

| 😱 Edge Cases | How to handle |
|--------------|---------------|
| **Multiple disconnected covers** | The corner set would have >4 corners – caught by the set check. |
| **Tiny rectangle overlaps** | Overlap causes duplicate corners → set size wrong. |
| **Negative coordinates** | Still works; just keep the min/max logic. |

---

## 🎯 Why You Should Master This Problem

- **Interview Gold‑mine** – It’s a classic “geometry + hash” question on LeetCode and many company interview stacks.
- **Showcase of Thinking** – Demonstrates you can reduce a 2‑D cover problem to algebra & set operations.
- **Coding‑style** – Clean, self‑contained, and easily explained in 5‑10 minutes.

---

## 📦 Code Solutions

Below are clean, fully‑commented implementations in **Java, Python, and C++**. Each follows the exact algorithm described.

---

### 1️⃣ Java

```java
import java.util.HashSet;
import java.util.Set;

/**
 * LeetCode 391 - Perfect Rectangle
 * Time:  O(n)
 * Space: O(n) – set of corners
 */
public class Solution {
    // Helper class to represent a point (x, y)
    private static class Point {
        int x, y;
        Point(int x, int y) { this.x = x; this.y = y; }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Point)) return false;
            Point p = (Point) o;
            return x == p.x && y == p.y;
        }

        @Override
        public int hashCode() {
            return 31 * x + y;
        }
    }

    public boolean isRectangleCover(int[][] rectangles) {
        // Bounding rectangle coordinates
        int minX = Integer.MAX_VALUE, minY = Integer.MAX_VALUE;
        int maxX = Integer.MIN_VALUE, maxY = Integer.MIN_VALUE;

        long totalArea = 0;          // Use long to avoid overflow
        Set<Point> cornerSet = new HashSet<>();

        for (int[] rect : rectangles) {
            int x1 = rect[0], y1 = rect[1];
            int x2 = rect[2], y2 = rect[3];

            // Update bounds
            minX = Math.min(minX, x1);
            minY = Math.min(minY, y1);
            maxX = Math.max(maxX, x2);
            maxY = Math.max(maxY, y2);

            // Accumulate area
            totalArea += (long)(x2 - x1) * (y2 - y1);

            // Process four corners
            Point[] corners = {
                new Point(x1, y1),
                new Point(x1, y2),
                new Point(x2, y1),
                new Point(x2, y2)
            };

            for (Point p : corners) {
                if (!cornerSet.add(p)) {   // already present -> remove
                    cornerSet.remove(p);
                }
            }
        }

        // After processing all rectangles
        if (cornerSet.size() != 4) return false;

        // These four corners must match the bounding rectangle corners
        if (!cornerSet.contains(new Point(minX, minY))) return false;
        if (!cornerSet.contains(new Point(minX, maxY))) return false;
        if (!cornerSet.contains(new Point(maxX, minY))) return false;
        if (!cornerSet.contains(new Point(maxX, maxY))) return false;

        // Area check
        long expectedArea = (long)(maxX - minX) * (maxY - minY);
        return totalArea == expectedArea;
    }
}
```

---

### 2️⃣ Python

```python
from typing import List, Tuple, Set

class Solution:
    def isRectangleCover(self, rectangles: List[List[int]]) -> bool:
        # Bounding box
        min_x, min_y = float('inf'), float('inf')
        max_x, max_y = float('-inf'), float('-inf')

        area = 0
        corners: Set[Tuple[int, int]] = set()

        for x1, y1, x2, y2 in rectangles:
            min_x, min_y = min(min_x, x1), min(min_y, y1)
            max_x, max_y = max(max_x, x2), max(max_y, y2)
            area += (x2 - x1) * (y2 - y1)

            # Four corners of the rectangle
            for corner in ((x1, y1), (x1, y2), (x2, y1), (x2, y2)):
                if corner in corners:
                    corners.remove(corner)
                else:
                    corners.add(corner)

        # After all rectangles processed
        if len(corners) != 4:
            return False

        expected_corners = {(min_x, min_y), (min_x, max_y),
                            (max_x, min_y), (max_x, max_y)}

        if corners != expected_corners:
            return False

        return area == (max_x - min_x) * (max_y - min_y)
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isRectangleCover(vector<vector<int>>& rectangles) {
        long long area = 0;
        long long minX = LLONG_MAX, minY = LLONG_MAX;
        long long maxX = LLONG_MIN, maxY = LLONG_MIN;

        // Set of corner points represented as pair<int,int>
        unordered_set<long long> corners;   // use hashing trick

        auto encode = [](int x, int y) -> long long {
            // 32-bit packing (works for coordinates within +/-1e5)
            return ((long long)(x + 100000) << 32) | (unsigned int)(y + 100000);
        };

        for (auto &r : rectangles) {
            int x1 = r[0], y1 = r[1];
            int x2 = r[2], y2 = r[3];

            minX = min(minX, (long long)x1);
            minY = min(minY, (long long)y1);
            maxX = max(maxX, (long long)x2);
            maxY = max(maxY, (long long)y2);

            area += (long long)(x2 - x1) * (y2 - y1);

            vector<pair<int,int>> pts = {{x1,y1},{x1,y2},{x2,y1},{x2,y2}};
            for (auto &p : pts) {
                long long key = encode(p.first, p.second);
                if (!corners.insert(key).second) { // already present -> remove
                    corners.erase(key);
                }
            }
        }

        if (corners.size() != 4) return false;

        vector<pair<long long,long long>> expected = {
            {minX, minY}, {minX, maxY},
            {maxX, minY}, {maxX, maxY}
        };
        for (auto &e : expected) {
            if (!corners.count(encode((int)e.first, (int)e.second))) return false;
        }

        long long expectedArea = (maxX - minX) * (maxY - minY);
        return area == expectedArea;
    }
};
```

> **Note on the C++ encoding trick** –  
> Using a 64‑bit integer to pack two 32‑bit coordinates avoids the need for a custom hash struct.  
> For the problem’s constraints (`|coordinate| ≤ 1e5`) the shift of 32 bits is safe.

---

## 🎯 Final Thoughts

- **Time & Space** – Linear and low‑overhead; perfect for a coding interview.  
- **Key Insight** – Reduce the 2‑D cover problem to *corner parity* + *area equality*.  
- **What to Highlight in an Interview** –  
  - Why the corner set ends up with exactly four corners.  
  - Why area check catches “gap” and “overlap” scenarios.  
  - Edge case handling (negative coordinates, large input).  

> 🚀 Mastering this question gives you a solid footing in geometric hashing problems, a common interview theme in top tech companies.

Happy coding, and may your cover be perfect! 🌟