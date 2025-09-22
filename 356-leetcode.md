---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 356. Line Reflection – The Complete Guide (Java | Python | C++)

> *“Can a single vertical line make all the points mirror‑image of themselves?”*  
> This is the question at the heart of Leetcode 356 – *Line Reflection*.  
> Below you’ll find a production‑ready solution in **Java, Python, and C++** plus a deep‑dive blog article that will help you land that coding interview job.

---

## 📌 Problem Recap

> **Given** a set of `n` points on a 2‑D plane, determine whether there exists a vertical line `x = k` such that reflecting every point across this line produces the **same set of points**.  
> Duplicate points are allowed.

```
Input  : points = [[1,1],[-1,1]]
Output : true  (reflect around x = 0)
```

```
Input  : points = [[1,1],[-1,-1]]
Output : false (no such vertical line)
```

*Constraints*  
- `1 ≤ n ≤ 10⁴`  
- `-10⁸ ≤ x, y ≤ 10⁸`

---

## 📈 “The Good, The Bad, and The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | `O(n)` | O(n²) brute force | O(n log n) sorting hack |
| **Space Complexity** | `O(n)` (hash set) | `O(1)` with two‑pointer but needs sorting | Extra memory for hash map of counts |
| **Intuition** | The reflection line is the mid‑point of the minimum and maximum `x` values. | People forget that the line may pass *between* two points. | Forget about duplicate points → wrong answer. |
| **Edge Cases** | Single point → always true. | Points lying on the same vertical line → `k` equals that line. | Overflow if using `int` for `minX + maxX`. Use `long`. |
| **Readability** | Simple set lookup. | HashMap of counts needed for duplicates. | Sorting & two pointers: hard to understand. |

---

## ⚙️ Algorithm (Hash‑Set + Midpoint)

1. **Find extremes**  
   - `minX = min(x for every point)`  
   - `maxX = max(x for every point)`  
2. **Determine potential line**  
   - `lineX = (minX + maxX) / 2.0`  
   - (Use a `double` or keep numerator as `long` to avoid precision loss.)
3. **Store all points in a set**  
   - Represent a point as a string `"x#y"` or a custom `Point` with proper `equals`/`hashCode`.
4. **Verify each point**  
   - For each `(x, y)`, compute mirror: `mirrorX = minX + maxX - x`.  
   - Check if `(mirrorX, y)` exists in the set.  
   - If any point fails → return `false`.
5. **All points passed** → return `true`.

**Why it works**  
- The line of symmetry must bisect the extreme left and right points.  
- If a point has a mirror, then every point’s pair must also be present.  
- Duplicates are automatically handled because the set contains the point itself.

**Complexity**  
- `O(n)` time, `O(n)` space.  
- No sorting, no `O(n²)` loops.

---

## 🧑‍💻 Code Implementations

> All three solutions use the same idea.  
> The Java & C++ versions keep a *hash set* of `long` pairs (`x << 32 | y`) for O(1) lookup.

---

### 1️⃣ Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public boolean isReflected(int[][] points) {
        if (points == null || points.length == 0) return true;

        int minX = Integer.MAX_VALUE, maxX = Integer.MIN_VALUE;
        Set<Long> pointSet = new HashSet<>();

        // Build set & find extremes
        for (int[] p : points) {
            int x = p[0], y = p[1];
            minX = Math.min(minX, x);
            maxX = Math.max(maxX, x);
            pointSet.add(encode(x, y));
        }

        long sum = (long) minX + (long) maxX;  // use long to avoid overflow

        // Verify each point
        for (int[] p : points) {
            int x = p[0], y = p[1];
            int mirrorX = (int) (sum - x);
            if (!pointSet.contains(encode(mirrorX, y))) {
                return false;
            }
        }
        return true;
    }

    // encode a point into a single long value
    private long encode(int x, int y) {
        return (((long) x) << 32) | (y & 0xffffffffL);
    }
}
```

---

### 2️⃣ Python

```python
class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        if not points:
            return True

        min_x = min(p[0] for p in points)
        max_x = max(p[0] for p in points)
        point_set = {(x, y) for x, y in points}
        s = min_x + max_x          # still int, Python handles big ints

        for x, y in points:
            mirror_x = s - x
            if (mirror_x, y) not in point_set:
                return False
        return True
```

---

### 3️⃣ C++

```cpp
#include <vector>
#include <unordered_set>
#include <utility>
#include <cstdint>

class Solution {
public:
    bool isReflected(std::vector<std::vector<int>>& points) {
        if (points.empty()) return true;

        int minX = INT_MAX, maxX = INT_MIN;
        std::unordered_set<uint64_t> pointSet;

        // Helper lambda to encode pair into 64‑bit key
        auto encode = [](int x, int y) -> uint64_t {
            return (static_cast<uint64_t>(static_cast<uint32_t>(x)) << 32)
                 | static_cast<uint32_t>(y);
        };

        for (const auto& p : points) {
            int x = p[0], y = p[1];
            minX = std::min(minX, x);
            maxX = std::max(maxX, x);
            pointSet.insert(encode(x, y));
        }

        long long sum = static_cast<long long>(minX) + maxX;  // long long to avoid overflow

        for (const auto& p : points) {
            int x = p[0], y = p[1];
            int mirrorX = static_cast<int>(sum - x);
            if (!pointSet.count(encode(mirrorX, y))) {
                return false;
            }
        }
        return true;
    }
};
```

---

## 📚 Blog Article Draft (SEO‑Optimized)

> **Title**: “Leetcode 356 – Line Reflection: Java, Python, & C++ Mastery (Interview‑Ready Guide)”  
> **Meta‑Description**: “Discover the O(n) solution for Leetcode 356 Line Reflection. Full code in Java, Python, and C++. Learn the pitfalls, edge cases, and interview tips.”

---

### 1. Introduction

- Quick problem statement.
- Real‑world analogy: mirror symmetry in design.
- Why it matters for algorithmic interviews.

### 2. Brute Force vs. Optimal

- Show the naive O(n²) approach.
- Discuss why it’s infeasible for `n = 10⁴`.

### 3. The Insight

- The reflection line must be the midpoint of the extreme `x` values.
- Use of `minX + maxX` as the invariant.

### 4. Building the Hash‑Set

- Represent a point as a composite key.
- Avoid collision with proper hashing (`(x << 32) | y`).

### 5. Verification Loop

- Compute `mirrorX = minX + maxX - x`.
- Lookup in the set – constant time.

### 6. Edge Cases & Common Mistakes

- Duplicate points.
- Overflow – use `long`/`long long`.
- Single point case.
- Points on the same vertical line.

### 7. Complexity Analysis

- `Time = O(n)`, `Space = O(n)`.

### 8. Code Walkthrough (Java)

- Highlight key lines, explain why each is needed.

### 9. Python & C++ Equivalents

- Discuss differences in data structures (`set` vs. `unordered_set`).

### 10. Interview Tips

- Explain the idea quickly: “Find the line, store all points, check mirrors.”
- Discuss alternative O(n log n) sorting approach.
- Talk about testing edge cases.

### 11. Takeaway

- Reinforce that understanding symmetry and hashing leads to clean, efficient solutions.

---

## 🚀 SEO & Job‑Hunter Boost

| SEO Element | How We Hit It |
|-------------|---------------|
| **Target Keywords** | “Leetcode 356”, “Line Reflection”, “Java solution”, “Python solution”, “C++ solution”, “hash set algorithm”, “job interview coding”, “algorithm interview” |
| **Content Length** | ~2000 words – rich for keyword density |
| **Headings & Sub‑Headings** | H1, H2, H3 to structure and signal relevance |
| **Code Blocks** | Highlighted syntax for readability |
| **Internal/External Links** | Link to Leetcode page, related problems |
| **Alt Text** | “Line Reflection Leetcode solution code” for images |
| **Canonical URL** | Ensure no duplicate content penalty |

> **Tip:** Add a short “FAQ” section at the end with questions like “What if points are floating‑point?” and “Can I use sorting instead?” – Google loves Q&A snippets.

---

## 🎓 Final Words

- **Good** – Simple, fast, and easy to implement.  
- **Bad** – The naive approach can mislead beginners.  
- **Ugly** – Forgetting to use `long` leads to overflow bugs.  

With the code snippets and the deep‑dive article, you’re equipped to ace Leetcode 356, impress interviewers, and climb that career ladder. Happy coding!