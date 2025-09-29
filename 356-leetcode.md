---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code Solutions

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++** that solve LeetCode 356 – *Line Reflection* in **O(n)** time and **O(n)** extra space.

---

### 1.1 Java – `Solution.java`

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    /**
     * Checks whether all points can be reflected across some vertical line.
     *
     * @param points 2‑D integer array of points. 0 ≤ points[i].length == 2
     * @return true if a vertical line of reflection exists, false otherwise
     */
    public boolean isReflected(int[][] points) {
        if (points == null || points.length == 0) return true;

        // Find the extreme x‑coordinates.
        int minX = Integer.MAX_VALUE;
        int maxX = Integer.MIN_VALUE;
        for (int[] p : points) {
            minX = Math.min(minX, p[0]);
            maxX = Math.max(maxX, p[0]);
        }

        // Sum of the extreme x‑coordinates = 2 * center of symmetry
        int sum = minX + maxX;

        // Store every point in a hash set for O(1) look‑ups.
        Set<Long> set = new HashSet<>();
        for (int[] p : points) {
            set.add(encode(p[0], p[1]));
        }

        // For every point, its mirror must also exist.
        for (int[] p : points) {
            int mirroredX = sum - p[0];
            long key = encode(mirroredX, p[1]);
            if (!set.contains(key)) {
                return false;          // missing partner – not symmetric
            }
        }
        return true;
    }

    /**
     * Encodes a pair (x, y) into a single long.
     * Assumes |x|, |y| ≤ 10^8 → fits comfortably into 32 bits each.
     */
    private long encode(int x, int y) {
        return (((long) x) << 32) ^ (y & 0xffffffffL);
    }
}
```

---

### 1.2 Python – `solution.py`

```python
from typing import List

class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        """Return True if the set of points is symmetric across a vertical line."""
        if not points:
            return True

        # Find extremes of the x‑axis.
        min_x = min(p[0] for p in points)
        max_x = max(p[0] for p in points)
        sum_x = min_x + max_x                     # 2 * center of symmetry

        # Store all points as tuples in a set for O(1) look‑ups.
        point_set = {tuple(p) for p in points}

        # Every point must have its mirror.
        for x, y in points:
            mirrored = (sum_x - x, y)
            if mirrored not in point_set:
                return False
        return True
```

---

### 1.3 C++ – `Solution.cpp`

```cpp
#include <vector>
#include <unordered_set>
#include <cstdint>

class Solution {
public:
    bool isReflected(std::vector<std::vector<int>>& points) {
        if (points.empty()) return true;

        // Find extreme x‑values.
        int minX = INT_MAX, maxX = INT_MIN;
        for (const auto& p : points) {
            minX = std::min(minX, p[0]);
            maxX = std::max(maxX, p[0]);
        }
        int sumX = minX + maxX;               // 2 * center of symmetry

        // Encode a point into a 64‑bit integer.
        auto encode = [](int x, int y) -> int64_t {
            return (static_cast<int64_t>(x) << 32) ^ static_cast<int64_t>(y) & 0xffffffffLL;
        };

        std::unordered_set<int64_t> S;
        for (const auto& p : points)
            S.insert(encode(p[0], p[1]));

        for (const auto& p : points) {
            int mirroredX = sumX - p[0];
            if (!S.count(encode(mirroredX, p[1])))
                return false;
        }
        return true;
    }
};
```

> **Why this works?**  
> The line of symmetry must be halfway between the smallest and largest `x` values.  
> If a point `(x, y)` exists, the reflected point must be `(minX + maxX - x, y)`.  
> Using a hash set gives constant‑time partner checks, so the whole algorithm is linear.

---

## 2.  Blog Article – “Line Reflection: The Good, The Bad, and The Ugly”

> **Meta‑Description (SEO‑friendly):**  
> Master LeetCode 356 – Line Reflection. Get a deep dive into the algorithm, code snippets in Java, Python & C++, plus insights on edge cases, performance, and interview tips. Boost your coding interview prep!

### 2.1 Introduction

When you sit down to solve LeetCode 356, *Line Reflection*, the first thought is probably “a sorting trick” or “two pointers”. In reality, there’s a far cleaner, O(n) solution that even passes 10⁴ points with flying colours. In this article we’ll dissect the problem, walk through the best approach, touch on the pitfalls (the ugly), and finish with ready‑to‑copy code for **Java**, **Python**, and **C++**.

### 2.2 Problem Statement (Restated)

> **Given**: `n` points on a 2‑D plane, `points[i] = [x_i, y_i]`.  
> **Task**: Determine if there exists a vertical line `x = k` such that reflecting all points across this line yields exactly the same set of points.

> Repeated points are allowed.

### 2.3 Intuition: The Mirror Line is Halfway Between Extremes

Think of the line as a mirror. For any point `(x, y)`, its reflection is `(k*2 - x, y)`. Notice the y‑coordinate stays the same – only x flips. Therefore, if you know the line’s **center**, you can instantly calculate a partner.

The center `k` is simply the average of the smallest and largest x‑coordinates:

```
k = (minX + maxX) / 2
```

Why? Because the leftmost and rightmost points must be symmetric about the mirror; otherwise, no single line can satisfy all points. This gives us an O(1) candidate for `k`.

### 2.4 The Elegant O(n) Solution (Hash Set)

1. **Find extremes** – traverse once to get `minX` and `maxX`.
2. **Compute sum** – `sumX = minX + maxX`.  
   *Note*: `sumX` equals `2 * k`. Working with `sumX` keeps us in integers and avoids floating point errors.
3. **Store all points** – hash each `(x, y)` pair. In Java/C++ we encode into a single 64‑bit integer; in Python a tuple is fine.
4. **Verify partners** – for every point `(x, y)` check whether `(sumX - x, y)` exists in the set.

If any point is missing its partner, the answer is `false`. Otherwise, we have perfect symmetry.

> **Why O(n)**:  
> - One pass to find extremes → O(n).  
> - One pass to build the set → O(n).  
> - One pass to check partners → O(n).  
> The hash set gives constant‑time lookup, so total is linear.

### 2.5 Edge Cases (The Ugly)

| Edge case | Why it matters | How the algorithm handles it |
|-----------|----------------|------------------------------|
| Duplicate points | They must all have partners (even with themselves). | Hash set stores every instance, so `count` works. |
| All points on the same vertical line | `minX = maxX` → `sumX = 2*minX`. | Each point’s partner is itself → passes. |
| Very large coordinates (±10⁸) | Prevent overflow. | Encoding uses 64‑bit integers (Java long / C++ long long). |
| `n = 1` | A single point is trivially symmetric. | The loop finds its own partner. |

### 2.6 Alternative Approaches (The Good)

- **Sorting + Two Pointers** – sort by x, then for each leftmost point pair it with the rightmost. Works in O(n log n).  
  *Pro*: No hash set needed.  
  *Con*: Extra log factor; code more verbose.

- **Map of y → Set of x** – group by y to handle large n faster in practice.  
  *Pro*: Handles huge datasets well.  
  *Con*: More complex.

### 2.7 Why This Matters for Interviews

- **Shows familiarity with hash structures** – a core data structure interview skill.  
- **Highlights understanding of geometry** – not just coding.  
- **Demonstrates clean, testable code** – essential for production interviews.

### 2.8 The Final Word (The Bad)

- **Beware of integer overflow** when summing `minX + maxX` if you’re using 32‑bit ints in languages like C++/Java.  
- **Remember to encode pairs**; using a string `"x#y"` is easy but slower.  
- **Edge cases** like repeated points or a single line of points can trip up naive solutions.

### 2.9 Code Snippets (Ready‑to‑Paste)

> **Java**  
> ```java
> public class Solution {
>     public boolean isReflected(int[][] points) {
>         // ... (code from section 1.1)
>     }
> }
> ```

> **Python**  
> ```python
> class Solution:
>     def isReflected(self, points: List[List[int]]) -> bool:
>         # ... (code from section 1.2)
> ```

> **C++**  
> ```cpp
> class Solution {
> public:
>     bool isReflected(vector<vector<int>>& points) {
>         // ... (code from section 1.3)
>     }
> };
> ```

### 2.10 Wrap‑Up

LeetCode 356 is deceptively simple but a great showcase of algorithmic thinking and clean code. The hash‑set trick gives you an **O(n)**, **O(n)** space solution that passes all edge cases and is interview‑ready. Use the snippets above to polish your portfolio, ace the interview, and maybe snag that next job offer. Happy coding!

--- 

**Keywords:** Line Reflection, LeetCode 356, vertical line symmetry, O(n) algorithm, hash set solution, Java, Python, C++, interview prep, coding interview, algorithmic problem solving.