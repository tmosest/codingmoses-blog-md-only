---
title: LeetCode 391. Perfect Rectangle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🛠️ LeetCode 391 – Perfect Rectangle  
## How to solve it (Java, Python, C++) + a job‑ready blog article

---

## 1. Problem recap  

> **391. Perfect Rectangle** – *Hard*  
> **Signature**  
> ```java
> public boolean isRectangleCover(int[][] rectangles)
> ```
>  
> You are given an array `rectangles` where each `rectangles[i] = [x1, y1, x2, y2]` represents a *axis‑aligned* rectangle.  
> Return **true** iff all the rectangles together form an **exact** cover of a single larger rectangle, i.e.  
> * the union of all small rectangles equals the large rectangle  
> * there are no gaps or overlaps.

> **Examples**  
> ```
> Input : [[1,1,3,3],[3,1,4,2],[3,2,4,4],[1,3,2,4],[2,3,3,4]]
> Output: true
> ```
> 
> ```
> Input : [[1,1,2,3],[1,3,2,4],[3,1,4,2],[3,2,4,4]]
> Output: false   // gap between the two blocks
> ```
> 
> ```
> Input : [[1,1,3,3],[3,1,4,2],[1,3,2,4],[2,2,4,4]]
> Output: false   // overlapping rectangles
> ```

---

## 2. The “corner set” algorithm (O(n) time, O(n) memory)

The most popular interview‑ready solution is based on two observations:

1. **Area check** –  
   The sum of the areas of all small rectangles must equal the area of the bounding rectangle.  
   Any overlap increases the sum, any gap decreases it.

2. **Corner check** –  
   For every rectangle we add its four corners to a *hash set*.  
   - If a corner is seen **for the first time** we insert it.  
   - If the same corner is seen again we *remove* it.

   After processing all rectangles the set will contain *exactly* the four corners of the bounding rectangle (the outermost rectangle).  
   Any interior corner will have been added and removed an even number of times, thus cancelled out.

**Why does it work?**  
- In a perfect tiling, each interior corner is shared by an even number (usually 4) of small rectangles, so it cancels out.  
- A boundary corner is shared only by the rectangles that touch the outer border, and in a perfect tiling exactly one rectangle contributes that corner (hence it stays in the set).  
- If any overlap or gap exists, the corner set will be *either* empty (overlap causing a corner to cancel incorrectly) *or* contain more than 4 points.

---

## 3. Code

Below you’ll find **three full solutions**: Java, Python, and C++.

> **Tip for interviewers:**  
> Mention that the algorithm works in **O(n)** time and **O(n)** space and that it’s easy to implement with a `HashSet` (Java), `set` (Python), or `unordered_set` (C++).  
> Show the corner‑encoding trick to keep the code short.

---

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public boolean isRectangleCover(int[][] rectangles) {
        long area = 0;                     // total area
        long minX = Long.MAX_VALUE, minY = Long.MAX_VALUE;
        long maxX = Long.MIN_VALUE, maxY = Long.MIN_VALUE;

        Set<Long> corners = new HashSet<>(); // encode as x*1e6+y

        for (int[] rect : rectangles) {
            long x1 = rect[0], y1 = rect[1];
            long x2 = rect[2], y2 = rect[3];

            // Update bounding rectangle
            minX = Math.min(minX, x1);
            minY = Math.min(minY, y1);
            maxX = Math.max(maxX, x2);
            maxY = Math.max(maxY, y2);

            // Sum of areas
            area += (x2 - x1) * (y2 - y1);

            // Add / remove corners
            addCorner(corners, x1, y1);
            addCorner(corners, x1, y2);
            addCorner(corners, x2, y1);
            addCorner(corners, x2, y2);
        }

        // Area of bounding rectangle
        long boundingArea = (maxX - minX) * (maxY - minY);
        if (area != boundingArea) return false; // area mismatch

        // Exactly four corners should remain
        if (corners.size() != 4) return false;

        // Check that remaining corners are exactly the bounding corners
        if (!corners.contains(encode(minX, minY))) return false;
        if (!corners.contains(encode(minX, maxY))) return false;
        if (!corners.contains(encode(maxX, minY))) return false;
        if (!corners.contains(encode(maxX, maxY))) return false;

        return true;
    }

    private void addCorner(Set<Long> set, long x, long y) {
        long key = encode(x, y);
        if (!set.add(key)) set.remove(key);
    }

    private long encode(long x, long y) {
        // 10^6 > coordinate range; safe packing
        return x * 1_000_000L + y;
    }
}
```

---

### 3.2 Python

```python
class Solution:
    def isRectangleCover(self, rectangles: List[List[int]]) -> bool:
        area = 0
        min_x = min_y = float('inf')
        max_x = max_y = float('-inf')
        corners = set()

        for x1, y1, x2, y2 in rectangles:
            # Bounding rectangle
            min_x, min_y = min(min_x, x1), min(min_y, y1)
            max_x, max_y = max(max_x, x2), max(max_y, y2)

            # Area sum
            area += (x2 - x1) * (y2 - y1)

            # Corner add / remove
            for c in [(x1, y1), (x1, y2), (x2, y1), (x2, y2)]:
                if c in corners:
                    corners.remove(c)
                else:
                    corners.add(c)

        # Area check
        if area != (max_x - min_x) * (max_y - min_y):
            return False

        # Corner check
        expected = {(min_x, min_y), (min_x, max_y),
                    (max_x, min_y), (max_x, max_y)}
        return corners == expected
```

---

### 3.3 C++

```cpp
#include <vector>
#include <unordered_set>
#include <utility>
#include <algorithm>

class Solution {
public:
    using ll = long long;
    struct pair_hash {
        size_t operator()(const pair<ll,ll>& p) const noexcept {
            return std::hash<ll>()(p.first ^ (p.second << 32));
        }
    };

    bool isRectangleCover(std::vector<std::vector<int>>& rectangles) {
        ll area = 0;
        ll minX = LLONG_MAX, minY = LLONG_MAX;
        ll maxX = LLONG_MIN, maxY = LLONG_MIN;
        std::unordered_set<std::pair<ll,ll>, pair_hash> corners;

        for (auto& r : rectangles) {
            ll x1=r[0], y1=r[1], x2=r[2], y2=r[3];

            minX = std::min(minX, x1);
            minY = std::min(minY, y1);
            maxX = std::max(maxX, x2);
            maxY = std::max(maxY, y2);

            area += (x2 - x1) * (y2 - y1);

            std::array<std::pair<ll,ll>,4> pts = {
                std::make_pair(x1,y1),
                std::make_pair(x1,y2),
                std::make_pair(x2,y1),
                std::make_pair(x2,y2)
            };

            for (auto& p : pts) {
                if (!corners.insert(p).second) corners.erase(p);
            }
        }

        ll expectedArea = (maxX - minX) * (maxY - minY);
        if (area != expectedArea) return false;

        std::unordered_set<std::pair<ll,ll>, pair_hash> expected = {
            {minX,minY},{minX,maxY},{maxX,minY},{maxX,maxY}
        };

        return corners == expected;
    }
};
```

---

## 4. “Good, Bad, Ugly” – an interview‑ready reflection

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time complexity** | **O(n)** – linear scan, no sorting. | - | - |
| **Space complexity** | **O(n)** – only a hash set of corners. | - | - |
| **Intuition** | Simple area + corner check – easy to explain. | If interviewer asks “why hash set?” you can say *corner cancellation property*. | Some candidates write complicated sweep‑line solutions; they’re *overkill* and harder to debug. |
| **Edge cases** | Handles negative coordinates, large ranges, many rectangles. | None in standard LeetCode tests. | Very small rectangles (zero area) can break naive solutions; be sure to validate `x1 < x2 && y1 < y2`. |
| **Potential pitfalls** | - Overflow on area: use 64‑bit. <br>- Key collision in hash: encode pair properly. | Forget to reset `minX/minY` and `maxX/maxY`. | Mistaking “removing corner if seen twice” as “remove every duplicate”; interior corners might cancel out *multiple* times – but the algorithm still works. |

---

## 5. SEO‑optimized blog title and meta description

### Title  
**"Perfect Rectangle LeetCode 391 – Interview‑Ready Solution in Java, Python & C++ (2025 Guide)"**

### Meta Description  
Discover how to crack LeetCode 391 (Perfect Rectangle) with a fast, memory‑efficient corner‑set algorithm. Get clean Java, Python, and C++ implementations, interview tips, and job‑search‑boosting insights. Perfect your coding interview prep today!

---

## 6. Why this article helps your job hunt

1. **Showcases algorithmic thinking** – You’re able to reduce a 2‑D covering problem to a simple set operation.  
2. **Highlights code quality** – Clean, readable code with proper edge‑case handling, a must‑have for senior roles.  
3. **Demonstrates language versatility** – Java, Python, C++ versions prove you can adapt to any stack.  
4. **Contains interview questions** – “Why use a hash set?” and “What if a corner is seen four times?” give you talking points.  
5. **SEO friendly** – Keywords like *Perfect Rectangle*, *LeetCode 391*, *interview*, *job prep* increase your article’s visibility on Google, bringing more recruiters to your profile.

---

## 7. Final take‑away

- **Always start with a bounding rectangle and area check.**  
- **Use a hash set to cancel out interior corners.**  
- **Test with large random data** to be sure you’re not hitting overflow.  
- **Prepare to explain the intuition** – interviewers love a clear narrative.

Good luck, and may your perfect rectangle cover all the gaps in your interview performance! 🚀