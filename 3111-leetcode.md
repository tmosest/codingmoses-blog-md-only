---
title: LeetCode 3111. Minimum Rectangles to Cover Points - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3111. Minimum Rectangles to Cover Points – Full Solutions + Blog Post

---

### 1. Problem Recap  
You’re given `points = [[x1,y1],[x2,y2], …]` and a width `w`.  
A rectangle can start at any `(x1,0)` and end at `(x2, y2)` **only** if `x2 – x1 ≤ w`.  
All points must lie inside **or on** at least one rectangle.  
Return the *minimum* number of rectangles needed.

> **Key Insight** – Because rectangles are horizontal strips that start at `y = 0`, only the **x‑coordinate** matters for covering.  
> Sort the points by `x` and greedily place a rectangle whenever we hit a point outside the current rectangle’s reach.

---

## 2. Algorithm (Sort + Greedy)

```
1. Sort points by their x‑coordinate ascending.
2. lastCoveredX = -∞ (any value < min(x))
3. result = 0
4. For each point (x, y) in sorted list:
        if x > lastCoveredX:
                // start a new rectangle
                result += 1
                lastCoveredX = x + w   // farthest x this rectangle can cover
5. Return result
```

### Why it works  
- After sorting, any point that appears after the current rectangle’s end can’t be covered by it.  
- The rectangle that starts at the current point and extends as far right as allowed (`x + w`) covers **all** subsequent points whose `x` ≤ this limit.  
- Greedy placement is optimal because delaying a rectangle can never reduce the total number.

---

## 3. Complexity

| Operation | Time | Space |
|-----------|------|-------|
|Sorting| `O(n log n)` | `O(1)` (in‑place) |
|Greedy scan| `O(n)` | `O(1)` |
|Total| **`O(n log n)`** | **`O(1)`** (apart from the input array) |

---

## 4. Reference Implementations  

### 4.1 Java

```java
import java.util.Arrays;
import java.util.Comparator;

public class Solution {
    public int minRectanglesToCoverPoints(int[][] points, int w) {
        // Sort by x-coordinate
        Arrays.sort(points, Comparator.comparingInt(a -> a[0]));

        int count = 0;
        long lastCoveredX = -1;          // safe for large values

        for (int[] p : points) {
            int x = p[0];
            if ((long) x > lastCoveredX) {    // new rectangle needed
                count++;
                lastCoveredX = (long) x + w;  // farthest x this rectangle can reach
            }
        }
        return count;
    }
}
```

### 4.2 Python

```python
from typing import List

class Solution:
    def minRectanglesToCoverPoints(self, points: List[List[int]], w: int) -> int:
        points.sort(key=lambda p: p[0])          # sort by x

        count = 0
        last_covered = -1                         # any value < min(x)

        for x, _ in points:
            if x > last_covered:                 # start new rectangle
                count += 1
                last_covered = x + w
        return count
```

### 4.3 C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int minRectanglesToCoverPoints(std::vector<std::vector<int>>& points, int w) {
        // sort by x
        std::sort(points.begin(), points.end(),
                  [](const std::vector<int>& a, const std::vector<int>& b) {
                      return a[0] < b[0];
                  });

        int count = 0;
        long long lastCovered = -1;            // safe for large values

        for (const auto& p : points) {
            if ((long long)p[0] > lastCovered) {
                ++count;
                lastCovered = (long long)p[0] + w;
            }
        }
        return count;
    }
};
```

All three solutions share the same core logic and run in `O(n log n)` time with `O(1)` extra space.

---

## 5. Blog Article – “Master LeetCode 3111: Minimum Rectangles to Cover Points”

> **Keywords**: LeetCode 3111, Minimum Rectangles to Cover Points, sort‑greedy algorithm, Java solution, Python solution, C++ solution, coding interview, algorithm interview, data structures, job interview, software engineer, technical interview

---

### Introduction

If you’re preparing for software engineering interviews, you’ll quickly run into problems that require a clean *sort‑and‑greedy* strategy. LeetCode 3111, **Minimum Rectangles to Cover Points**, is a classic example. In this article, I’ll walk through the problem, show the optimal greedy solution, discuss edge cases, present ready‑to‑copy code in Java, Python, and C++, and explain why this problem is a great interview talking point.

---

### Problem Statement

Given:
- `points = [[x1, y1], [x2, y2], …]` – all points are unique.
- A width `w`.

Each rectangle must have:
- Lower corner on the x‑axis: `(x1, 0)`.
- Upper corner anywhere above: `(x2, y2)` with `x2 - x1 ≤ w`.

Goal: **Cover every point with the fewest rectangles possible**.

---

### Why This Problem Is Interesting

1. **Dimension Reduction** – Although each point has an `x` and `y`, the `y` value is irrelevant for coverage because all rectangles extend from `y = 0` upwards.  
2. **Greedy Optimality** – A well‑chosen greedy choice (starting a rectangle at the leftmost uncovered point) guarantees optimality.  
3. **Large Input** – `n` can be up to 100 000, so an `O(n log n)` solution is required.  
4. **Real‑World Insight** – This pattern is common in scheduling, interval covering, and resource allocation problems.

---

### The Greedy Insight

Sort points by their x‑coordinate. Then scan from left to right:

- If the current point lies beyond the reach of the current rectangle (`x > lastCoveredX`), **start a new rectangle** at this point.
- The rectangle will span from `x` to `x + w`, so set `lastCoveredX = x + w`.

This ensures that each rectangle covers as many points as possible to its right.

---

### Implementation Walk‑Through

#### Java

```java
import java.util.Arrays;
import java.util.Comparator;

public class Solution {
    public int minRectanglesToCoverPoints(int[][] points, int w) {
        // 1. Sort by x
        Arrays.sort(points, Comparator.comparingInt(a -> a[0]));

        int res = 0;
        long last = -1;                 // any value < min(x)

        for (int[] p : points) {
            int x = p[0];
            // 2. If this point is not covered, start a new rectangle
            if ((long) x > last) {
                res++;
                last = (long) x + w;   // reach of the new rectangle
            }
        }
        return res;
    }
}
```

**What to Highlight in an Interview**

- Use `long` for `lastCoveredX` to guard against overflow (`x + w` may be up to 2 × 10⁹).  
- Explain that sorting with `Arrays.sort` is *stable* enough because only `x` matters.

#### Python

```python
class Solution:
    def minRectanglesToCoverPoints(self, points: List[List[int]], w: int) -> int:
        points.sort(key=lambda p: p[0])    # sort by x

        res = 0
        last = -1

        for x, _ in points:
            if x > last:
                res += 1
                last = x + w
        return res
```

**Interview Tip** – Show that Python’s built‑in `sort` is `Timsort` (optimal `O(n log n)`).

#### C++

```cpp
class Solution {
public:
    int minRectanglesToCoverPoints(vector<vector<int>>& points, int w) {
        // Sort by x
        sort(points.begin(), points.end(),
             [](const auto& a, const auto& b) { return a[0] < b[0]; });

        int count = 0;
        long long last = -1;   // use long long for safety

        for (const auto& p : points) {
            if ((long long)p[0] > last) {
                ++count;
                last = (long long)p[0] + w;
            }
        }
        return count;
    }
};
```

---

### Edge Cases & Common Pitfalls

| Edge Case | What to Watch For |
|-----------|-------------------|
|`w = 0`| Each rectangle can cover only one point. The algorithm still works.|
|Very large `x + w` (≈ 2 × 10⁹)| Use 64‑bit integers (`long` / `long long`) to avoid overflow.|
|Points with identical `x` values| Problem guarantees uniqueness, but if not, sorting still works; duplicates just share the same rectangle.|
|Empty input (unlikely on LeetCode)| Return `0` – our loop naturally handles it.|
|Negative coordinates| Sorting handles negatives just fine; `lastCoveredX` starts at `-1`.|

---

### Testing Strategy

| Test | Points | w | Expected | Reason |
|------|--------|---|----------|--------|
|1 | `[[1,5],[2,3],[5,1]]` | `3` | `1` | One rectangle `[1,4]` covers all. |
|2 | `[[1,1],[4,1],[6,2],[9,4]]` | `2` | `2` | Two rectangles: `[1,3]` and `[6,8]`. |
|3 | `[[10,0],[20,0],[30,0]]` | `5` | `3` | Width too small to cover more than one point. |
|4 | Large random array (10⁵ points) | `10⁵` | Runs in < 1 s | Confirms performance. |

---

### Take‑Away for Interviews

- **State the Intuition**: “The y‑coordinate never matters because all rectangles start at y = 0.”
- **Explain Optimality**: Show that starting a rectangle at the leftmost uncovered point can’t be improved by any other placement.
- **Time & Space**: Mention that sorting dominates the time, and we’re only using constant extra memory.
- **Language‑agnostic**: You can present the algorithm in any language. It’s a great demonstration of a clean O(n log n) greedy algorithm.
- **Talk About Edge Cases**: Mention overflow and negative coordinates. It shows you’re thinking about production robustness.

---

### Final Thoughts

LeetCode 3111 is a deceptively simple problem that packs several interviewable concepts:
- Sorting  
- Greedy decision making  
- Interval covering  
- Edge‑case handling  

Mastering it not only gives you a ready‑to‑use solution for the LeetCode platform but also demonstrates your ability to *reduce dimensionality* and *apply a proven greedy strategy*—skills that hiring managers love to see. Good luck on your next technical interview!  

--- 

### References  

- LeetCode Problem 3111 – *Minimum Rectangles to Cover Points*  
- Official Solution Discussions (Java / Python / C++)  
- Interview‑prep blogs on greedy algorithms and interval covering  

--- 

Happy coding, and may your rectangles always be optimal!