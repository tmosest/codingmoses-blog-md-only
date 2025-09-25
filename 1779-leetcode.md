---
title: LeetCode 1779. Find Nearest Point That Has the Same X or Y Coordinate - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1779 – Find Nearest Point That Has the Same X or Y Coordinate  
**LeetCode 1779 | Easy**  
| Language | Code | Complexity |
|----------|------|------------|
| **Java** | `public int nearestValidPoint(int x, int y, int[][] points)` | `O(n)` time, `O(1)` space |
| **Python** | `def nearestValidPoint(x: int, y: int, points: List[List[int]]) -> int` | `O(n)` time, `O(1)` space |
| **C++** | `int nearestValidPoint(int x, int y, vector<vector<int>>& points)` | `O(n)` time, `O(1)` space |

---

## 🚀 Code Solutions

### Java

```java
class Solution {
    public int nearestValidPoint(int x, int y, int[][] points) {
        int minDistance = Integer.MAX_VALUE;   // keeps track of the smallest Manhattan distance
        int bestIndex   = -1;                  // -1 indicates "no valid point found"

        for (int i = 0; i < points.length; i++) {
            int px = points[i][0];
            int py = points[i][1];

            // A point is valid only if it shares the same x or y coordinate.
            if (px == x || py == y) {
                int distance = Math.abs(px - x) + Math.abs(py - y);

                // Update when we find a strictly smaller distance
                if (distance < minDistance) {
                    minDistance = distance;
                    bestIndex   = i;
                }
            }
        }

        return bestIndex;   // will be -1 if no valid point exists
    }
}
```

### Python

```python
from typing import List

class Solution:
    def nearestValidPoint(self, x: int, y: int, points: List[List[int]]) -> int:
        min_dist = float('inf')
        best_idx = -1

        for i, (px, py) in enumerate(points):
            if px == x or py == y:
                dist = abs(px - x) + abs(py - y)
                if dist < min_dist:
                    min_dist = dist
                    best_idx = i

        return best_idx
```

### C++

```cpp
class Solution {
public:
    int nearestValidPoint(int x, int y, vector<vector<int>>& points) {
        int minDist = INT_MAX;
        int bestIdx = -1;

        for (int i = 0; i < points.size(); ++i) {
            int px = points[i][0];
            int py = points[i][1];

            if (px == x || py == y) {
                int dist = abs(px - x) + abs(py - y);
                if (dist < minDist) {
                    minDist = dist;
                    bestIdx = i;
                }
            }
        }
        return bestIdx;
    }
};
```

---

## 📚 Problem Recap & Constraints

- **Input**: current point `(x, y)` and a list `points` of `N` points, each `[ai, bi]`.  
- **Valid Point**: shares either the same `x` or the same `y`.  
- **Goal**: return the index of the *closest* valid point by Manhattan distance.  
- **Tie‑break**: if two points are equally close, choose the one with the *smallest index*.  
- **No valid point**: return `-1`.  
- **Constraints**  
  - `1 ≤ N ≤ 10^4`  
  - `1 ≤ x, y, ai, bi ≤ 10^4`  

---

## 🔍 The Good, The Bad, and The Ugly

| Aspect | What It Means | How It Affects Interviews |
|--------|---------------|---------------------------|
| **The Good** | - **O(N) time** – linear scan, no sorting or extra data structures. <br>- **O(1) space** – we only store a few integers. <br>- Handles edge cases (e.g., the point itself, no valid point). | Shows you can reason about constraints, pick the simplest algorithm, and keep memory usage minimal – a common interview expectation. |
| **The Bad** | - **Manhattan distance calculation** can be a source of off‑by‑one errors if you forget the absolute value. <br>- Mistaking “same `x` **or** same `y`” for “both” is a classic trap. | Minor coding mistakes may cause a wrong answer, so it’s vital to test the logic thoroughly. |
| **The Ugly** | - **Index handling**: initializing `bestIdx` to `0` would break the “no valid point” case; you need `-1`. <br>- **Tie‑breaking**: ignoring the smallest index when distances are equal results in a WA on test cases like `[[1, 2], [1, 2]]`. | These subtle bugs are often the reason a seemingly correct solution gets marked wrong. Always write unit tests to cover edge cases. |

---

## 🧠 How to Reason About the Problem in an Interview

1. **Clarify the problem statement**  
   - “Do we include the current point itself?” → Yes, it can be a valid point.  
   - “What if two points are at the same distance?” → Return the lower index.

2. **Think about constraints**  
   - `N` up to `10^4` → An `O(N^2)` solution will TLE.  
   - Coordinates are small positive ints → No special data structure needed.

3. **Draft a brute‑force approach**  
   - Loop over each point, compute distance if valid.  
   - Keep track of min distance and index.  

4. **Proof of optimality**  
   - Each point is examined once → Linear time is optimal because every point could be the answer.

5. **Discuss space optimization**  
   - No need for auxiliary containers; just keep a couple of ints.

6. **Walk through a sample test case**  
   - Show how you handle tie‑breaking and “no valid point”.

---

## 💡 Edge Cases to Test

| Test | Input | Expected |
|------|-------|----------|
| 1 | `x = 3, y = 4, points = [[3, 4]]` | `0` (same location) |
| 2 | `x = 3, y = 4, points = [[2,3]]` | `-1` (no valid) |
| 3 | `x = 3, y = 4, points = [[3,1],[2,4],[4,4]]` | `2` (distance tie, lower index) |
| 4 | `x = 5, y = 5, points = [[5,5],[5,5],[5,5]]` | `0` (first occurrence) |
| 5 | `x = 1, y = 1, points = [[2,3],[4,5]]` | `-1` |

---

## 📈 SEO & Job‑Search Tips

- **Keyword‑rich title**: “LeetCode 1779: Find Nearest Point with Same X or Y – Java, Python & C++ Solutions”
- **Meta description**: “Learn how to solve LeetCode 1779 – Find Nearest Point that Shares X or Y Coordinate. Detailed Java, Python, and C++ solutions, complexity analysis, and interview tips.”
- **Headings**: Use H2 tags for *Problem Recap*, *Code Solutions*, *Complexity Analysis*, *The Good, The Bad, and The Ugly*, *Interview Strategy*.
- **Internal links**: Link to other related LeetCode problems you’ve solved (e.g., “Nearest Point” series).
- **External links**: Cite official LeetCode problem URL, and the solution references you mentioned.
- **Structured data**: Add JSON‑LD for article SEO (optional for a blog).

---

## 🎯 Takeaway

- **Simple Linear Scan** is all you need:  
  ```text
  for each point:
      if same x or same y:
          compute Manhattan distance
          update best if distance < current min
  ```
- **Initialize index to -1** to differentiate “no valid point” from “index 0”.  
- **Tie‑breaking**: keep the first occurrence; no need for extra comparisons.  

This solution runs in **O(N)** time, **O(1)** space, and passes all edge cases. Mastering such problems will make your technical interview portfolio shine and give you the confidence to tackle the next challenge.

Happy coding, and good luck on your next interview! 🚀