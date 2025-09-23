---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. LeetCode 356 – **Line Reflection**  
> “Given n points on a 2‑D plane, find if there exists a vertical line (parallel to the y‑axis) that reflects the points symmetrically.”  

Below you will find **ready‑to‑copy solutions** in **Java**, **Python**, and **C++** (all O(n) time, O(n) space).  
After the code we’ll dive into a **SEO‑friendly blog post** that breaks the problem into *the good, the bad, and the ugly* – perfect for interview prep, blog outreach, and SEO ranking.

---

## 2. Solutions

> **Core Idea**  
> 1. The reflection line must be located exactly halfway between the minimum and maximum x‑coordinate.  
> 2. For every point `(x, y)` the reflected point must be `(2*midX – x, y)` and must also exist in the input set.  
> 3. Use a hash‑set / hash‑map to achieve O(1) lookup.

> **Why this works**  
> - If a line exists, all points must pair up with a mirror image, forcing the line to lie at the arithmetic mean of the extreme x‑values.  
> - The converse is also true – if all pairs exist for this `midX`, the set is symmetric.

> **Corner Cases**  
> - Duplicate points are fine – they simply map to themselves.  
> - Single‑point sets are trivially symmetric.  
> - Negative coordinates and large values up to 10⁸ are handled with 64‑bit integers.

---

### 2.1 Java

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

        // Midpoint (may be fractional, so keep as double)
        double midX = (minX + maxX) / 2.0;

        // Store all points in a HashSet of string "x#y"
        Set<String> set = new HashSet<>();
        for (int[] p : points) {
            set.add(p[0] + "#" + p[1]);
        }

        // Verify each reflected counterpart exists
        for (int[] p : points) {
            double reflectedX = 2 * midX - p[0];
            // Use exact integer check – reflectedX must be integral
            if (reflectedX != (long) reflectedX) return false;
            String key = (long) reflectedX + "#" + p[1];
            if (!set.contains(key)) return false;
        }

        return true;
    }
}
```

**Key Points**

* `midX` is kept as `double` to support odd sums (`minX + maxX` odd).
* The reflected x must be an integer; otherwise symmetry is impossible.
* HashSet lookup is O(1), giving O(n) overall.

---

### 2.2 Python

```python
class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        if not points:
            return True

        xs = [p[0] for p in points]
        min_x, max_x = min(xs), max(xs)
        mid_x = (min_x + max_x) / 2.0

        point_set = {(p[0], p[1]) for p in points}

        for x, y in points:
            reflected = 2 * mid_x - x
            if reflected not in point_set or reflected % 1 != 0:
                return False

        return True
```

* Uses a `set` of tuples for O(1) membership test.  
* The `reflected % 1 != 0` guard catches non‑integral reflection candidates.

---

### 2.3 C++

```cpp
#include <vector>
#include <unordered_set>
#include <tuple>

class Solution {
public:
    bool isReflected(std::vector<std::vector<int>>& points) {
        if (points.empty()) return true;

        int minX = INT_MAX, maxX = INT_MIN;
        for (const auto& p : points) {
            minX = std::min(minX, p[0]);
            maxX = std::max(maxX, p[0]);
        }

        double midX = (minX + maxX) / 2.0;

        // Encode point as 64‑bit key: ((int64_t)x << 32) | (y & 0xffffffff)
        auto encode = [](int x, int y) -> uint64_t {
            return (static_cast<uint64_t>(static_cast<int32_t>(x)) << 32) |
                   (static_cast<uint32_t>(static_cast<int32_t>(y)));
        };

        std::unordered_set<uint64_t> s;
        for (auto& p : points) s.insert(encode(p[0], p[1]));

        for (auto& p : points) {
            double reflectedX = 2 * midX - p[0];
            if (reflectedX != static_cast<long long>(reflectedX)) return false;
            int rx = static_cast<int>(reflectedX);
            if (!s.count(encode(rx, p[1]))) return false;
        }
        return true;
    }
};
```

* Uses bit‑shifting to pack a pair `(x, y)` into a 64‑bit key for efficient lookup.  
* Handles large coordinates safely.

---

## 3. Blog Post – “Line Reflection (LeetCode 356): The Good, The Bad, and The Ugly”

> **SEO Keywords** – *LeetCode 356, Line Reflection, coding interview, Java, Python, C++, hash set, vertical symmetry, algorithm, time complexity, O(n) solution, interview tips, algorithmic thinking*.

---

### 3.1 Introduction

> In software engineering interviews, **symmetry** often turns into a fun little puzzle.  
> LeetCode 356 – *Line Reflection* – asks whether a set of 2‑D points can be mirrored over a vertical line.  
> Despite the “medium” tag, the solution is a **single‑pass O(n)** trick that every developer should know.

---

### 3.2 Problem Recap

```
Given points = [[x1, y1], [x2, y2], ...]
Return true  if ∃ line x = L such that reflecting every point over L
             results in the original set.
Return false otherwise.
```

*Duplicate points are allowed.*  
*Constraints*: 1 ≤ n ≤ 10⁴, |xᵢ|, |yᵢ| ≤ 10⁸.

---

### 3.3 The Good: A Clean O(n) Solution

1. **Compute the potential mirror line**  
   * `L = (minX + maxX) / 2`.  
   * Rationale: For symmetry, every point’s x must be mirrored around the same `L`. The furthest left and right points enforce this `L`.

2. **Store all points in a hash‑based set**  
   * Allows O(1) lookup of reflected points.  
   * Encode as `"x#y"` or as a 64‑bit key in C++.

3. **Validate every point**  
   * For each `(x, y)` compute `rx = 2*L - x`.  
   * If `rx` is not an integer, symmetry is impossible.  
   * Check that `(rx, y)` exists in the set.

4. **Return** `true` only if all points pass the check.

**Why it’s good**  
* **Linear time** – one pass for min/max, one pass for validation.  
* **Linear space** – a single set of n points.  
* **Robust** – handles negative numbers, duplicates, and large coordinates.

---

### 3.4 The Bad: Common Pitfalls

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Using floating‑point equality** | `2*L - x` might produce a tiny rounding error (e.g., 3.0000001). | Use integer arithmetic when possible. Compute `mid = minX + maxX` as an integer and compare with `2*x`. |
| **Ignoring duplicates** | A duplicate point might map to itself, but if the line is wrong you might incorrectly think symmetry holds. | Still store duplicates in the set; the lookup logic will catch missing counterparts. |
| **Using a list instead of a set** | O(n²) time when checking for each reflected point. | Use a hash‑set or unordered_set. |
| **Assuming the line is at zero** | Only works for special cases. | Compute the actual `mid` from min/max x. |
| **Failing to check integerness** | For odd sum of extremes, the line can be at `.5` (e.g., `x=0.5`). A point at `x=0` reflects to `x=1`. The algorithm must accept fractional `L`. | Keep `L` as a double but ensure `rx` is an integer (or check `mid*2 == 2*x + 2*rx`). |

---

### 3.5 The Ugly: When Symmetry Breaks

* **Large coordinate ranges**: With `|x| ≤ 10⁸`, adding two ints can overflow 32‑bit.  
  * **Solution**: Use 64‑bit (long in Java, long long in C++) for `mid` and for the encoded key.

* **Non‑integer reflection**: If `minX + maxX` is odd, `L` will be a half‑integer.  
  * **Solution**: Perform all calculations in *double* or keep `mid` as `minX + maxX` (an integer) and check `2*x + 2*rx == mid*2`.

* **Memory overhead for very large n**: Storing a string `"x#y"` for every point can be memory heavy.  
  * **Solution**: In C++ pack the coordinates into a 64‑bit integer. In Java, use a custom `Point` class with overridden `hashCode`/`equals`.

---

### 3.6 Variations & Extensions

| Variation | How to Tackle |
|-----------|---------------|
| **Horizontal reflection** | Mirror over `y = L`; swap the roles of x and y. |
| **Reflection over an arbitrary line** | Requires more geometry: compute line coefficients, transform points, and check symmetry. |
| **Dynamic updates (add/delete points)** | Maintain a hash‑set and update min/max x on each change; O(1) amortized. |
| **Large dataset streaming** | Use a streaming min/max algorithm and an external hash structure (e.g., Bloom filter) for memory efficiency. |

---

### 3.7 Time & Space Complexity

| Implementation | Time | Space |
|----------------|------|-------|
| Java/Python/C++ O(n) hash‑set | **O(n)** | **O(n)** |
| Brute‑force pairwise check | **O(n²)** | **O(n)** |
| Sorting based (O(n log n)) | **O(n log n)** | **O(n)** |

**Why O(n) wins**  
With n up to 10⁴, O(n²) is ~10⁸ comparisons – far too slow for interview time limits. The hash‑set trick gives a clean O(n) solution that is easily explainable.

---

### 3.8 Interview Tips

1. **Explain the intuition first** – talk about the extreme points and the idea of a center line.  
2. **Show the formula** – `L = (minX + maxX) / 2`.  
3. **Discuss the set lookup** – “We’ll encode each point as a key and then for every point check its mirror exists.”  
4. **Mention edge cases** – single point, duplicate points, odd sum, overflow.  
5. **Show complexity** – Linear time and space.

---

### 3.9 Conclusion

> LeetCode 356 is a classic *symmetry* problem that tests both **geometric insight** and **hash‑map mastery**.  
> With the O(n) solution above, you’ll be able to nail the problem in any interview and show that you can think in both algorithmic and engineering terms.

---

### 3.10 Further Reading

* [LeetCode 356 – Line Reflection](https://leetcode.com/problems/line-reflection/)  
* [Java HashSet for 2‑D points](https://www.baeldung.com/java-hashset)  
* [Python tuple hashing](https://docs.python.org/3/tutorial/datastructures.html#hashable-objects)  
* [C++ unordered_set with custom hash](https://stackoverflow.com/questions/1393147/how-to-store-pair-in-unordered-set)  

---

> **Want more interview‑ready code?** Subscribe to our newsletter for weekly LeetCode walk‑throughs, coding patterns, and interview hacks!  

---

### 3.11 SEO Summary

* **Target keywords**: *LeetCode 356*, *Line Reflection*, *vertical symmetry algorithm*, *hash set*, *O(n) solution*, *Java coding interview*, *Python interview problem*, *C++ hash map*, *time complexity*, *algorithmic thinking*.  
* **Meta description** (≈155 chars):  
  “Master LeetCode 356 – Line Reflection with O(n) solutions in Java, Python, and C++. Learn the geometry trick, handle edge cases, and ace coding interviews.”  

--- 

Happy coding! 🚀