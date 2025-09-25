---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap (LeetCode 356 – **Line Reflection**)

> **Goal**:  
> Given an array of 2‑D points `points[i] = [x, y]`, determine whether there exists a vertical line (parallel to the y‑axis) that reflects every point onto another point in the set.  
> The line may contain points of the set, and duplicate points are allowed.

**Constraints**

| Constraint | Value |
|------------|-------|
| `n == points.length` | 1 ≤ n ≤ 10⁴ |
| `points[i][j]` | –10⁸ ≤ points[i][j] ≤ 10⁸ |

The problem is a classic “reflection” check and is solvable in **O(n)** time and **O(n)** extra space by using a hash set.

---

## 2.  Solution Overview (O(n) HashSet)

1. **Compute the potential reflection line**  
   - For any reflection line `x = k`, the average of every pair of reflected points must equal `k`.  
   - Therefore, if a line exists, `k` is the same for all points, and it equals  
     \[
     k = \frac{\minX + \maxX}{2}
     \]
     where `minX` and `maxX` are the smallest and largest x‑coordinates in the list.

2. **Verify the reflection**  
   - Store all points in a hash set (using a string key `"x#y"` or a custom tuple hash).  
   - For each point `(x, y)`, compute its mirrored coordinate  
     \[
     x_{\text{mirror}} = 2k - x
     \]  
     and check whether `(x_{\text{mirror}}, y)` exists in the set.  
   - If any mirrored point is missing, the reflection is impossible.

Because the set lookup is O(1), the total complexity remains O(n).

---

## 3.  Reference Code

Below are **Java, Python, and C++** implementations that follow the algorithm above.  
All codes use the same logic: find the candidate line using min/max x, then verify each point.

---

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public boolean isReflected(int[][] points) {
        if (points == null || points.length == 0) return true;

        // Find min and max x
        int minX = Integer.MAX_VALUE;
        int maxX = Integer.MIN_VALUE;
        for (int[] p : points) {
            minX = Math.min(minX, p[0]);
            maxX = Math.max(maxX, p[0]);
        }

        // The potential reflection line is the midpoint between minX and maxX
        // We keep the sum to avoid double precision issues.
        int sum = minX + maxX; // 2 * k

        // Build hash set of points
        Set<Long> pointSet = new HashSet<>();
        for (int[] p : points) {
            long key = ((long)p[0] << 32) | (p[1] & 0xffffffffL);
            pointSet.add(key);
        }

        // Verify each point
        for (int[] p : points) {
            int x = p[0], y = p[1];
            int reflectedX = sum - x;
            long reflectedKey = ((long)reflectedX << 32) | (y & 0xffffffffL);
            if (!pointSet.contains(reflectedKey)) return false;
        }
        return true;
    }

    // Simple main for quick manual testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[][] p1 = {{1,1},{-1,1}};
        int[][] p2 = {{1,1},{-1,-1}};
        System.out.println(sol.isReflected(p1)); // true
        System.out.println(sol.isReflected(p2)); // false
    }
}
```

---

### 3.2 Python

```python
from typing import List, Set, Tuple

class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        if not points:
            return True

        xs = [p[0] for p in points]
        min_x, max_x = min(xs), max(xs)
        sum_x = min_x + max_x           # 2 * k

        point_set: Set[Tuple[int, int]] = {(x, y) for x, y in points}

        for x, y in points:
            reflected_x = sum_x - x
            if (reflected_x, y) not in point_set:
                return False
        return True


# Quick manual test
if __name__ == "__main__":
    sol = Solution()
    print(sol.isReflected([[1, 1], [-1, 1]]))      # True
    print(sol.isReflected([[1, 1], [-1, -1]]))    # False
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isReflected(vector<vector<int>>& points) {
        if (points.empty()) return true;

        int minX = INT_MAX, maxX = INT_MIN;
        for (auto &p : points) {
            minX = min(minX, p[0]);
            maxX = max(maxX, p[0]);
        }

        long long sumX = (long long)minX + maxX;   // 2 * k

        unordered_set<long long> pointSet;
        pointSet.reserve(points.size() * 2);
        for (auto &p : points) {
            long long key = ((long long)p[0] << 32) ^ (p[1] & 0xffffffffLL);
            pointSet.insert(key);
        }

        for (auto &p : points) {
            int x = p[0], y = p[1];
            int reflectedX = (int)(sumX - x);
            long long reflectedKey = ((long long)reflectedX << 32) ^ (y & 0xffffffffLL);
            if (!pointSet.count(reflectedKey)) return false;
        }
        return true;
    }
};
```

---

## 4.  Blog Article – “Line Reflection: The Good, The Bad, & The Ugly (And Why It Makes a Great Interview Question)”

> **SEO Keywords**: `line reflection`, `LeetCode 356`, `vertical line reflection`, `hash set solution`, `O(n) algorithm`, `job interview coding`, `Java/Python/C++ solution`, `data structures interview`

---

### 4.1 Introduction

If you’re preparing for a software engineering interview, you’ll quickly discover that *symmetry problems* are a favourite of interviewers. One of the most common is **LeetCode 356 – Line Reflection**. This post walks through the problem, shows a clean O(n) solution in Java, Python, and C++, and explains why this question is a great showcase for data‑structure skills.

---

### 4.2 Problem Statement (Short & Sweet)

> **Given** a list of 2‑D points, determine if there exists a vertical line `x = k` that reflects every point onto another point in the set.  
> *Duplicate points are allowed.*  

**Example**  
- `[[1, 1], [-1, 1]]` → **True** (line `x = 0`)  
- `[[1, 1], [-1, -1]]` → **False**

---

### 4.3 Why It’s a Great Interview Question

| Aspect | What Interviewers Look For |
|--------|----------------------------|
| **Mathematical Insight** | Spotting the “midpoint of minX and maxX” trick. |
| **Data‑Structure Choice** | Using a hash set for O(1) point look‑ups. |
| **Time Complexity** | Achieving O(n) instead of O(n²) brute force. |
| **Language Agnostic** | Solvable in any language, so candidates can demonstrate their favorite stack. |
| **Edge Cases** | Duplicates, negative coordinates, very large values (need 64‑bit handling). |

---

### 4.4 The “Good”: A Clean O(n) Algorithm

1. **Compute `k` from extremes**  
   ```text
   k = (minX + maxX) / 2
   ```
   In integer math we keep `sum = minX + maxX` to avoid floating point.

2. **Hash All Points**  
   Store every `(x, y)` as a key in a hash set.  
   *Why?* Because we’ll look up the reflected counterpart in constant time.

3. **Verify Each Point**  
   For every `(x, y)`, compute the mirrored x:
   ```text
   mirroredX = sum - x
   ```
   Check that `(mirroredX, y)` exists in the set.

If all points pass, the reflection line exists.

**Complexity**  
- Time: **O(n)** – single pass to find extremes, one pass to insert into set, one pass to verify.  
- Space: **O(n)** – the hash set holds all points.

---

### 4.5 The “Bad”: Common Pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Using a list for look‑ups** | `O(n)` per query → total `O(n²)` | Use a hash set or map. |
| **Floating‑point comparison** | Precision errors when `sum` is odd | Keep `sum` as an integer; use integer arithmetic. |
| **Overflow** | `minX + maxX` may exceed 32‑bit int | Use 64‑bit (`long`/`long long`). |
| **Neglecting duplicates** | Some solutions assume unique points | Hash set inherently handles duplicates. |
| **Assuming line is always `x = 0`** | Wrong for asymmetrical point sets | Compute the true midpoint. |

---

### 4.6 The “Ugly”: An O(n²) Brute Force

A naïve solution pairs every point with every other point, tries all candidate lines, and checks reflection. That leads to quadratic time and is impractical for `n = 10⁴`. This serves as a cautionary example of why **hashing + linear passes** is the gold standard.

---

### 4.7 Language‑Specific Tips

| Language | Recommendation |
|----------|----------------|
| **Java** | Use `HashSet<Long>` with bit‑shifting to combine `x` and `y`. Keep `sum` as `int` and cast to `long` when necessary. |
| **Python** | Set of tuples `set((x, y) for x, y in points)`; integer arithmetic is unlimited precision. |
| **C++** | `unordered_set<long long>` with a custom key `(x << 32) ^ y`. Reserve space to avoid rehashing. |

---

### 4.8 How This Helps Your Job Hunt

- **Showcase Algorithmic Thinking**: The solution demonstrates a clear *problem‑to‑solution* pipeline, which is highly prized by hiring managers.
- **Data‑Structure Mastery**: Using a hash set for look‑ups shows you know when and why to pick the right container.
- **Code Quality**: Clean, well‑commented, and language‑idiomatic code is a signal of professionalism.
- **Interview Confidence**: Practicing this problem will help you tackle similar symmetry and reflection questions in interviews.

---

### 4.9 Conclusion

LeetCode 356 is deceptively simple but packs a lot of interview value. A fast, clean solution using a hash set and a single-pass computation of the reflection line is the optimal way to solve it. Avoid the pitfalls of quadratic time, floating‑point errors, and overflow, and you’ll have a winning explanation ready for any technical interview.

> **Ready to impress?** Clone the reference code snippets above, run them on the test cases, and add your own optimizations. Good luck with your job hunt! 🚀

---

### 4.10 References

- LeetCode 356 – [Line Reflection](https://leetcode.com/problems/line-reflection/)
- Common interview patterns: hash set, symmetry, mid‑point, O(n) algorithms.

---

Happy coding!