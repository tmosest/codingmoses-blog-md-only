---
title: LeetCode 391. Perfect Rectangle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview – LeetCode 391 “Perfect Rectangle”

| **Title** | **Difficulty** | **Link** |
|-----------|----------------|----------|
| Perfect Rectangle | Hard | <https://leetcode.com/problems/perfect-rectangle/> |

**Statement**  
You are given a list of axis‑aligned rectangles. Each rectangle is described by `[x1, y1, x2, y2]`, the bottom‑left and top‑right corners.  
Return `true` iff the rectangles together cover *exactly* one larger rectangle – no overlaps, no gaps, and the union of all rectangles equals a single axis‑aligned rectangle.

**Why this problem is important**  
- The solution uses a classic “count‑the‑corners” trick that appears in many interview questions (e.g., “Number of Islands”, “Valid Sudoku”).  
- It tests your ability to reason about area, boundaries, and set operations – all key skills in system design and large‑scale data processing.  
- It is a great way to demonstrate clean coding style in Java, Python, and C++, showing you can solve the same problem in multiple languages – a big plus for a multi‑stack hiring manager.

---

## 2.  Core Idea – Area + Corner Set

1. **Area check** –  
   *The sum of the areas of all small rectangles must equal the area of the bounding rectangle formed by the minimal and maximal coordinates.*  
   This catches many failure modes (gap, overlap, extra area) quickly.

2. **Corner set trick** –  
   For every rectangle add its four corners to a set.  
   - If a corner appears **exactly once** it must be a corner of the final big rectangle.  
   - If it appears **twice** (or any even number) it is an internal edge and cancels out.  
   After processing all rectangles, the set should contain **exactly four corners** – the four corners of the big rectangle.  
   Any other number indicates an overlap or a gap.

The combination of the two checks guarantees correctness.

---

## 3.  Implementation – Java, Python, C++

### 3.1 Java

```java
import java.util.*;

class Solution {
    public boolean isRectangleCover(int[][] rectangles) {
        long area = 0;                 // 64‑bit to avoid overflow
        long minX = Long.MAX_VALUE, minY = Long.MAX_VALUE;
        long maxX = Long.MIN_VALUE, maxY = Long.MIN_VALUE;

        Set<Long> corners = new HashSet<>();

        for (int[] r : rectangles) {
            long x1 = r[0], y1 = r[1], x2 = r[2], y2 = r[3];

            // Update bounding rectangle
            minX = Math.min(minX, x1);
            minY = Math.min(minY, y1);
            maxX = Math.max(maxX, x2);
            maxY = Math.max(maxY, y2);

            // Sum area
            area += (x2 - x1) * (y2 - y1);

            // Encode corner as a single long: (x << 32) | y
            long p1 = (x1 << 32) ^ y1;
            long p2 = (x1 << 32) ^ y2;
            long p3 = (x2 << 32) ^ y1;
            long p4 = (x2 << 32) ^ y2;

            // Add/remove corners
            addCorner(corners, p1);
            addCorner(corners, p2);
            addCorner(corners, p3);
            addCorner(corners, p4);
        }

        // Bounding rectangle area must match sum of small areas
        long boundingArea = (maxX - minX) * (maxY - minY);
        if (area != boundingArea) return false;

        // Only four corners should remain
        if (corners.size() != 4) return false;

        // All four expected corners must be present
        long corner1 = (minX << 32) ^ minY;
        long corner2 = (minX << 32) ^ maxY;
        long corner3 = (maxX << 32) ^ minY;
        long corner4 = (maxX << 32) ^ maxY;

        return corners.contains(corner1) && corners.contains(corner2) &&
               corners.contains(corner3) && corners.contains(corner4);
    }

    private void addCorner(Set<Long> set, long point) {
        if (!set.add(point)) {
            set.remove(point);   // remove when seen twice
        }
    }
}
```

### 3.2 Python

```python
class Solution:
    def isRectangleCover(self, rectangles: List[List[int]]) -> bool:
        area = 0
        min_x = min_y = float('inf')
        max_x = max_y = float('-inf')
        corners = set()

        for x1, y1, x2, y2 in rectangles:
            # Update bounding rectangle
            min_x, min_y = min(min_x, x1), min(min_y, y1)
            max_x, max_y = max(max_x, x2), max(max_y, y2)

            # Accumulate area
            area += (x2 - x1) * (y2 - y1)

            # Corner tuples
            pts = {(x1, y1), (x1, y2), (x2, y1), (x2, y2)}
            for p in pts:
                if p in corners:
                    corners.remove(p)
                else:
                    corners.add(p)

        # Area must match
        if area != (max_x - min_x) * (max_y - min_y):
            return False

        # Exactly four corners left
        if len(corners) != 4:
            return False

        # They must be the four corners of the bounding box
        expected = {(min_x, min_y), (min_x, max_y),
                    (max_x, min_y), (max_x, max_y)}
        return corners == expected
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isRectangleCover(vector<vector<int>>& rectangles) {
        long long area = 0;
        long long minX = LLONG_MAX, minY = LLONG_MAX;
        long long maxX = LLONG_MIN, maxY = LLONG_MIN;

        unordered_set<long long> corners;

        auto addCorner = [&](long long key) {
            if (!corners.insert(key).second) corners.erase(key);
        };

        for (auto &r : rectangles) {
            long long x1 = r[0], y1 = r[1], x2 = r[2], y2 = r[3];

            minX = min(minX, x1);
            minY = min(minY, y1);
            maxX = max(maxX, x2);
            maxY = max(maxY, y2);

            area += (x2 - x1) * (y2 - y1);

            // Encode point as single 64‑bit: (x << 32) ^ y
            long long p1 = (x1 << 32) ^ y1;
            long long p2 = (x1 << 32) ^ y2;
            long long p3 = (x2 << 32) ^ y1;
            long long p4 = (x2 << 32) ^ y2;

            addCorner(p1); addCorner(p2);
            addCorner(p3); addCorner(p4);
        }

        if (area != (maxX - minX) * (maxY - minY)) return false;
        if (corners.size() != 4) return false;

        long long c1 = (minX << 32) ^ minY;
        long long c2 = (minX << 32) ^ maxY;
        long long c3 = (maxX << 32) ^ minY;
        long long c4 = (maxX << 32) ^ maxY;

        return corners.count(c1) && corners.count(c2) &&
               corners.count(c3) && corners.count(c4);
    }
};
```

> **Why the hash trick works**  
> A point that appears an even number of times (two, four, …) is internal – all those occurrences cancel. A point that appears once is a boundary corner. The algorithm guarantees that only the outer corners survive.

---

## 4.  Good, Bad, and Ugly – What You Should Highlight in an Interview

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Area Check** | Handles huge gaps/overlaps instantly; O(n) | Can mislead if you forget the bounding area | Over‑reliance on area alone may miss subtle overlap configurations |
| **Corner Set** | Clean O(n) logic; no geometry libraries | Requires careful hashing; easy to mis‑encode | Corner‑set approach can be confusing to interviewers unfamiliar with the trick |
| **Time Complexity** | `O(n)` | No significant drawback | None |
| **Space Complexity** | `O(n)` (set of corners) | Extra memory usage | None |
| **Edge Cases** | Works for negative coordinates, large ranges | Forgetting to use 64‑bit area | Wrong corner encoding causing false negatives |
| **Readability** | Comments help | Long lambda may obscure logic | Unclear variable names may obscure intent |

**Tips for the Interviewer’s Lens**

1. **Explain the intuition** – “We’re basically counting how many times each corner appears.”  
2. **Show the area math** – “Sum of areas must equal bounding rectangle area.”  
3. **Address overflow** – “We use 64‑bit to stay safe with coordinates up to ±10⁵.”  
4. **Discuss corner encoding** – “We use a unique 64‑bit key to store a point.”  
5. **Mention test cases** – “Test a single big rectangle, a perfect grid, a missing gap, and an overlap.”  

---

## 5.  Complexity Analysis

| **Operation** | **Java** | **Python** | **C++** |
|---------------|----------|------------|---------|
| Time | `O(n)` | `O(n)` | `O(n)` |
| Space | `O(n)` | `O(n)` | `O(n)` |
| Notes | 64‑bit `long` for area; 64‑bit `long` key for corners | Python `int` is arbitrary precision; set of tuples | `long long` and `unordered_set` |

`n` is the number of rectangles (`1 ≤ n ≤ 2·10⁴`).

---

## 6.  SEO‑Optimized Blog Article – “Perfect Rectangle: Master the Hard LeetCode 391”

### 6.1 Headline & Meta

- **Title:** Perfect Rectangle – LeetCode 391 Solution (Java, Python, C++)  
- **Meta Description:** Learn the optimal algorithm for LeetCode 391 “Perfect Rectangle.” Code in Java, Python, and C++. Understand the area + corner trick, time/space complexity, and interview tips.

### 6.2 Introduction

> **“In a world of messy data, finding the exact shape is a job skill.”**  
> The *Perfect Rectangle* problem tests your ability to think geometrically and mathematically under constraints. It is a staple of technical interviews for backend, data‑engineering, and algorithmic roles.

### 6.3 Problem Recap

Summarize the problem with a small diagram (text‑only) and highlight constraints.

### 6.4 Why It Matters for Your Job Hunt

- **Cross‑Language Proficiency:** Show you can solve the same logic in Java, Python, and C++ – a must‑have for many hiring managers.  
- **Set Operations:** Interviewers love set‑based solutions because they reveal your data‑structure intuition.  
- **Edge‑Case Thinking:** Demonstrates robustness – you think about overflow, negative coordinates, and minimal/maximum values.

### 6.5 The Winning Strategy – Area + Corner Set

Provide a step‑by‑step narrative, visualizing how corners cancel and why the bounding rectangle must match the summed area.

### 6.6 Code Walkthrough

Show each language version, explain:

- **Corner encoding** (hashing a pair into a single key).  
- **Set toggling** (add if not present, remove if present).  
- **Area calculation** with 64‑bit integers.

### 6.7 Good, Bad, Ugly

Discuss the interview‑friendly aspects and pitfalls. Emphasize clarity, naming, and edge‑case handling.

### 6.8 Performance Metrics

Tabulate time/space complexity. Mention that the solution runs in linear time – ideal for `n = 20,000`.

### 6.9 Practice & Interview Prep

- Run through the three examples.  
- Create custom test cases: perfect grid, single gap, large overlap, negative coordinates.  
- Re‑implement from scratch without looking at the code to solidify the logic.

### 6.10 Final Take‑away

> **The Perfect Rectangle problem is not just a coding challenge; it’s a showcase of analytical thinking, data‑structure savvy, and coding discipline across languages. Master it, and you’re ready to tackle any algorithm interview.**

---

## 7.  Final Checklist for the Interview

- ✔️ **Explain the area + corner trick.**  
- ✔️ **Show working code in Java, Python, and C++.**  
- ✔️ **Mention 64‑bit safety for coordinates.**  
- ✔️ **Demonstrate edge‑case handling.**  
- ✔️ **Discuss time/space complexity and why it’s optimal.**  

Good luck—now you have the perfect rectangle (and your code) ready for any hard technical interview!