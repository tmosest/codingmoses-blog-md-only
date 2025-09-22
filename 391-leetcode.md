---
title: LeetCode 391. Perfect Rectangle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 391. Perfect Rectangle – A Complete Cross‑Language Solution

**LeetCode ID**: 391  
**Difficulty**: Hard  
**Tags**: Hash Table, Geometry, Set, Two‑Dimensional Geometry  

---

### Why is this problem interesting?

At a glance it looks like a simple “check coverage” task, but it hides subtle pitfalls:

* **Overlaps** – two tiny rectangles can cover the same area twice.
* **Gaps** – a tiny rectangle may leave a hole in the big rectangle.
* **Corner mishandling** – all four corners of the big rectangle must appear exactly once in the whole set.

To pass the tests you have to think **geometrically** and **set‑wise**.  
Below you’ll find a clean O(n) solution in **Java, Python, and C++** that will help you ace this question in a technical interview (or on LeetCode).

---

## The Optimal Strategy

The classical, well‑known solution uses two observations:

1. **Area check** – The sum of all small rectangles’ areas must equal the area of the bounding rectangle.
2. **Corner set check** – If you add every corner point to a set and toggle its presence each time you encounter it, the only points that should survive are the four corners of the bounding rectangle. Any extra corner indicates either an overlap or a gap.

### Algorithm Steps

1. **Initialize**  
   * `minX, minY` to `+∞`  
   * `maxX, maxY` to `-∞`  
   * `areaSum` to `0`  
   * `cornerSet` as an empty set

2. **Iterate over each rectangle `[x1, y1, x2, y2]`**  
   * Update `minX, minY, maxX, maxY` with the rectangle’s edges.  
   * Add its area `(x2 - x1) * (y2 - y1)` to `areaSum`.  
   * For each of its four corners `(x1, y1)`, `(x1, y2)`, `(x2, y1)`, `(x2, y2)`:  
     * If the point exists in `cornerSet`, remove it.  
     * Otherwise, add it.

3. **Validate**  
   * **Area**: `areaSum == (maxX - minX) * (maxY - minY)`  
   * **Corners**: `cornerSet` contains exactly the four points:  
     `(minX, minY), (minX, maxY), (maxX, minY), (maxX, maxY)`.

If both checks pass → **true**; otherwise → **false**.

---

## Code – Java

```java
import java.util.*;

public class Solution {
    public boolean isRectangleCover(int[][] rectangles) {
        long areaSum = 0;
        long minX = Long.MAX_VALUE, minY = Long.MAX_VALUE;
        long maxX = Long.MIN_VALUE, maxY = Long.MIN_VALUE;

        Set<Long> cornerSet = new HashSet<>();

        for (int[] rect : rectangles) {
            long x1 = rect[0], y1 = rect[1], x2 = rect[2], y2 = rect[3];

            // Update bounding rectangle
            minX = Math.min(minX, x1);
            minY = Math.min(minY, y1);
            maxX = Math.max(maxX, x2);
            maxY = Math.max(maxY, y2);

            // Area
            areaSum += (x2 - x1) * (y2 - y1);

            // Corner handling
            toggleCorner(cornerSet, x1, y1);
            toggleCorner(cornerSet, x1, y2);
            toggleCorner(cornerSet, x2, y1);
            toggleCorner(cornerSet, x2, y2);
        }

        // Bounding rectangle area
        long boundingArea = (maxX - minX) * (maxY - minY);
        if (areaSum != boundingArea) return false;

        // Expected four corners
        Set<Long> expected = new HashSet<>(Arrays.asList(
                encode(minX, minY),
                encode(minX, maxY),
                encode(maxX, minY),
                encode(maxX, maxY)
        ));

        return cornerSet.equals(expected);
    }

    private void toggleCorner(Set<Long> set, long x, long y) {
        long key = encode(x, y);
        if (!set.add(key)) set.remove(key);   // if add returns false, key existed -> remove
    }

    // Encode a pair (x,y) into a single long.  We shift x 32 bits to the left.
    private long encode(long x, long y) {
        return (x << 32) ^ y;
    }
}
```

**Why it works**

* `areaSum` is a `long` – the coordinates can be up to 10⁵, so the area can reach 10¹⁰, which fits in a 64‑bit integer.
* Corner toggling guarantees that overlapped corners cancel out, while the true four corners of the big rectangle remain.
* The final equality check ensures that there are no gaps or extra corners.

---

## Code – Python

```python
from typing import List

class Solution:
    def isRectangleCover(self, rectangles: List[List[int]]) -> bool:
        min_x, min_y = float('inf'), float('inf')
        max_x, max_y = float('-inf'), float('-inf')
        area_sum = 0
        corners = set()

        for x1, y1, x2, y2 in rectangles:
            # Bounding rectangle
            min_x, min_y = min(min_x, x1), min(min_y, y1)
            max_x, max_y = max(max_x, x2), max(max_y, y2)

            # Area
            area_sum += (x2 - x1) * (y2 - y1)

            # Toggle corners
            for corner in [(x1, y1), (x1, y2), (x2, y1), (x2, y2)]:
                if corner in corners:
                    corners.remove(corner)
                else:
                    corners.add(corner)

        # Bounding rectangle area
        if area_sum != (max_x - min_x) * (max_y - min_y):
            return False

        # Expected corners
        expected = {(min_x, min_y), (min_x, max_y),
                    (max_x, min_y), (max_x, max_y)}
        return corners == expected
```

*Python’s* built‑in `set` makes corner toggling trivial.  
The `float('inf')` trick avoids an explicit large constant for initial bounds.

---

## Code – C++

```cpp
#include <vector>
#include <unordered_set>
#include <tuple>
#include <algorithm>
using namespace std;

class Solution {
public:
    bool isRectangleCover(vector<vector<int>>& rectangles) {
        long long areaSum = 0;
        long long minX = LLONG_MAX, minY = LLONG_MAX;
        long long maxX = LLONG_MIN, maxY = LLONG_MIN;

        unordered_set<long long> corners;

        for (auto &r : rectangles) {
            long long x1 = r[0], y1 = r[1], x2 = r[2], y2 = r[3];

            // Bounding rectangle
            minX = min(minX, x1);
            minY = min(minY, y1);
            maxX = max(maxX, x2);
            maxY = max(maxY, y2);

            // Area
            areaSum += (x2 - x1) * (y2 - y1);

            // Toggle corners
            toggle(corners, x1, y1);
            toggle(corners, x1, y2);
            toggle(corners, x2, y1);
            toggle(corners, x2, y2);
        }

        // Check area
        long long boundingArea = (maxX - minX) * (maxY - minY);
        if (areaSum != boundingArea) return false;

        // Expected four corners
        unordered_set<long long> expected = {
            encode(minX, minY),
            encode(minX, maxY),
            encode(maxX, minY),
            encode(maxX, maxY)
        };

        return corners == expected;
    }

private:
    void toggle(unordered_set<long long> &s, long long x, long long y) {
        long long key = encode(x, y);
        if (!s.insert(key).second) { // insert failed -> key existed
            s.erase(key);
        }
    }

    long long encode(long long x, long long y) {
        return (x << 32) ^ y;   // x shifted 32 bits to left; works for 32‑bit signed ints
    }
};
```

*Why the `encode` trick works:*  
`x` and `y` are within ±10⁵, so after shifting `x` left by 32 bits the lower 32 bits are free for `y`. This guarantees a unique 64‑bit key for every coordinate pair.

---

## Blog Article – “The Perfect Rectangle: The Good, The Bad, The Ugly”

### 1. Introduction

> **“You need to cover a rectangle with smaller rectangles without overlaps or gaps.”**  
> A problem that looks deceptively simple turns into a delightful interview challenge.  
> In this post we’ll dissect LeetCode 391 – *Perfect Rectangle* – in depth and walk through a clean, production‑ready solution in **Java**, **Python**, and **C++**. We’ll also cover the hidden pitfalls (the *bad* and the *ugly*) that trip many candidates.

**SEO Keywords**: Perfect Rectangle Leetcode 391, Java solution, Python solution, C++ solution, interview question, coding interview, hash set, geometry problem, tech interview.

### 2. Problem Recap

You’re given `rectangles`, each represented as `[x1, y1, x2, y2]` (bottom‑left to top‑right).  
Return `true` if all rectangles together form *exactly* one larger rectangle with no holes or overlaps.

### 3. The “Good” – Elegant Observations

1. **Area Consistency** – The sum of small areas must equal the bounding rectangle’s area.
2. **Corner Magic** – If you add every corner to a set and toggle its presence each time, only the four outer corners should survive.

These two checks together eliminate all “bad” configurations: overlaps, gaps, and duplicate corners.

### 4. The “Bad” – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Integer overflow** | Coordinates up to 10⁵ → area up to 10¹⁰. | Use 64‑bit (`long`/`long long`). |
| **Corner collision** | Overlapping rectangles share corners, leading to extra points. | Toggle corners in a set. |
| **Floating‑point inaccuracies** | Using doubles for area comparison can cause precision errors. | Stick to integer arithmetic. |
| **Wrong bounding rectangle** | Forgetting to update min/max for every rectangle. | Update inside the loop. |

### 5. The “Ugly” – Edge Cases You Might Miss

* **Zero‑area rectangles** – Should never appear given the constraints (`x1 < x2`, `y1 < y2`).  
* **Negative coordinates** – Works fine with the integer approach.  
* **Large dataset** – Up to 20,000 rectangles: O(n) time and O(n) memory is mandatory.

### 6. Full Solution Walkthrough (Java)

(Insert the Java code block from above, annotated line by line.)

> **Why this passes all LeetCode tests?**  
> *Area* ensures global coverage.  
> *Corner set* guarantees local consistency.  
> Combined, they form a complete invariant for the perfect cover.

### 7. Parallel Implementations

*Python* – Use tuple `set` and `float('inf')` for bounds.  
*C++* – Use `unordered_set<long long>` with a bit‑shifting key.

(Insert the Python and C++ code blocks.)

### 8. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Iterating rectangles | **O(n)** | |
| Corner toggling (set ops) | **O(1)** average per op | |
| Final corner check | **O(1)** | |
| Total | **O(n)** | **O(n)** (for corner set) |

### 9. How to Test Locally

```python
def test():
    sol = Solution()
    assert sol.isRectangleCover([[1,1,3,3],[3,1,4,2],[3,2,4,4],[1,3,2,4],[2,3,3,4]]) == True
    assert sol.isRectangleCover([[1,1,2,3],[1,3,2,4],[3,1,4,2],[3,2,4,4]]) == False
    assert sol.isRectangleCover([[1,1,3,3],[3,1,4,2],[1,3,2,4],[2,2,4,4]]) == False
```

### 10. Takeaway for Interviewers

- **Explain the intuition** before diving into code.  
- **Show your thought process**: area + corner set.  
- **Discuss edge cases**: overflow, negative coordinates, etc.  
- **Write clean, readable code** in the language of choice.

### 11. Wrap‑Up & Call‑to‑Action

If you cracked this problem, you just mastered a classic LeetCode interview gold‑mine. Keep practicing with variants – *covering a rectangle with squares*, *checking for tiling with dominoes*, etc.  

**Looking to land your next tech role?**  
> Share your solution on LinkedIn, tag your recruiter, and let the hiring managers know you can solve hard geometry problems efficiently.  

Happy coding! 🚀

--- 

**Happy learning!**