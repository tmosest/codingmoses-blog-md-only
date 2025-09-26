---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 356 – Line Reflection  
**Language‑agnostic solution (Java / Python / C++)**  
**SEO‑Optimized Blog Post – “Line Reflection on LeetCode: A Deep Dive + Interview Tips”**  

---

### 1. Problem Recap (LeetCode 356)

> **Given a set of points on the 2‑D plane, determine whether there exists a vertical line `x = k` that reflects every point onto another point in the set.**  
> Repeated points are allowed.  

**Input**

```text
points = [[x1, y1], [x2, y2], …, [xn, yn]]
```

**Output**

```text
true  // if such a line exists
false // otherwise
```

**Constraints**

* `1 ≤ n ≤ 10⁴`
* `-10⁸ ≤ points[i][j] ≤ 10⁸`

---

## 2. Intuition

For a vertical reflection line `x = k` the x‑coordinate of a point `p = (x, y)` maps to `x' = 2k – x`.  
So for every point we must be able to find a *matching* point with the same y and the reflected x.  
The key observation:

> **The sum `x + x'` is constant for all matched pairs → `k = (x + x') / 2`.**  

Thus, if all points share the same “center” value `center = (minX + maxX) / 2`, the reflection is possible.

---

## 3. Optimal O(n) Solution – Hash Map

1. **Find `minX` and `maxX`.**  
2. **Compute `center = minX + maxX`** (we keep the doubled center to stay in integers).  
3. **Group points by y‑coordinate.**  
   * For each y, we store a multiset of its x values (a `Map<Integer, Integer>` in Java, `Counter` in Python, or `unordered_map<int,int>` in C++).  
4. **For each y‑group**  
   * For every x, check that the reflected coordinate `center - x` exists with the same multiplicity.  
   * If any mismatch occurs → return `false`.  
5. **If all groups match → return `true`.**

The algorithm runs in **O(n)** time and **O(n)** extra space.

---

## 4. Code (Java, Python, C++)

### 4.1 Java – HashMap Approach

```java
import java.util.*;

public class Solution {
    public boolean isReflected(int[][] points) {
        if (points == null || points.length == 0) return true;

        int minX = Integer.MAX_VALUE, maxX = Integer.MIN_VALUE;
        for (int[] p : points) {
            minX = Math.min(minX, p[0]);
            maxX = Math.max(maxX, p[0]);
        }

        // doubled center to keep everything integer
        int center = minX + maxX;

        // Map<y, Map<x, count>>
        Map<Integer, Map<Integer, Integer>> map = new HashMap<>();
        for (int[] p : points) {
            map.computeIfAbsent(p[1], k -> new HashMap<>())
                .merge(p[0], 1, Integer::sum);
        }

        for (Map<Integer, Integer> xs : map.values()) {
            for (int x : xs.keySet()) {
                int reflectX = center - x;
                int cntX = xs.getOrDefault(x, 0);
                int cntR = xs.getOrDefault(reflectX, 0);
                if (cntX != cntR) return false;
            }
        }
        return true;
    }
}
```

### 4.2 Python – Counter Approach

```python
from collections import defaultdict, Counter

class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        if not points:
            return True

        xs = [p[0] for p in points]
        center = min(xs) + max(xs)          # doubled center

        # group by y
        groups = defaultdict(list)
        for x, y in points:
            groups[y].append(x)

        for xs in groups.values():
            cnt = Counter(xs)
            for x, c in cnt.items():
                r = center - x
                if cnt[x] != cnt.get(r, 0):
                    return False
        return True
```

### 4.3 C++ – unordered_map Approach

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

class Solution {
public:
    bool isReflected(std::vector<std::vector<int>>& points) {
        if (points.empty()) return true;

        int minX = INT_MAX, maxX = INT_MIN;
        for (auto &p : points) {
            minX = std::min(minX, p[0]);
            maxX = std::max(maxX, p[0]);
        }
        int center = minX + maxX; // doubled center

        // map<y, unordered_map<x, count>>
        std::unordered_map<int, std::unordered_map<int, int>> mp;
        for (auto &p : points) {
            mp[p[1]][p[0]]++;
        }

        for (auto &entry : mp) {
            auto &cnts = entry.second;
            for (auto &kv : cnts) {
                int x = kv.first;
                int r = center - x;
                if (cnts[x] != cnts[r]) return false;
            }
        }
        return true;
    }
};
```

---

## 5. Blog Article – “Line Reflection on LeetCode: A Deep Dive + Interview Tips”

### 5.1 Title & Meta

**Title:**  
`Line Reflection on LeetCode (356) – Easy to Hard? A Step‑by‑Step Java / Python / C++ Guide + Interview Tips`

**Meta Description:**  
`Solve LeetCode 356 “Line Reflection” in Java, Python, and C++ with an O(n) hash‑map solution. Learn the trick, edge cases, time/space complexity, and interview hacks. Great for CS interviews and boosting your résumé!`

---

### 5.2 Introduction

If you’re preparing for a software engineering interview, you’ll quickly realize that geometry problems are surprisingly common.  
One of the classics in the LeetCode “Medium” range is **Line Reflection (356)**.  
It’s deceptively simple but hides a neat trick that transforms a quadratic brute force into a linear‑time solution.

In this post we’ll:

1. Explain the problem in plain English.  
2. Walk through the optimal O(n) solution and why it works.  
3. Show working code in Java, Python, and C++.  
4. Discuss edge cases and potential pitfalls.  
5. Offer interview‑style talking points to help you ace the discussion.  
6. Highlight “good, bad, ugly” from a coding‑practice perspective.

By the end, you’ll be ready to type a clean solution on the whiteboard or in a live coding session.

---

### 5.3 Problem Statement (Re‑written)

> **Given a set of 2‑D points, determine whether there exists a vertical line `x = k` that reflects every point onto another point in the set.**  
> Duplicate points are allowed.  
> Return `true` if such a line exists, otherwise `false`.

---

### 5.4 Intuition: Center of Reflection

When a point `(x, y)` reflects over `x = k`, it lands on `(2k - x, y)`.  
If a perfect reflection exists, **all matched pairs share the same `k`**.  
Thus, if we can find a constant `center = 2k` such that for every point `x` the value `center - x` also appears, we’re good.

Instead of guessing `k`, we can *derive* it from the extremes:

* The farthest left point `minX` and farthest right point `maxX` must be mirrored around the line.  
* Therefore, `center = minX + maxX` (note: this is **twice** the true `k`, but we can keep everything in integers).  

If every point has a counterpart `center - x` with the same y, the reflection is valid.

---

### 5.5 Detailed Algorithm (O(n))

| Step | Action | Reason |
|------|--------|--------|
| 1 | Compute `minX`, `maxX` | To find the candidate reflection line (`center = minX + maxX`). |
| 2 | Group points by y | Points must pair with the same y; grouping avoids cross‑y mismatches. |
| 3 | For each y‑group, count occurrences of each x | Repeated points must have matching counts. |
| 4 | For every x in a group, check `center - x` exists with equal count | Ensures the reflection property holds for that y. |
| 5 | If all groups pass, return true | Successful reflection found. |

**Complexity**

* **Time:** O(n) – single pass to find extremes + single pass through all points for grouping + linear checks within groups.  
* **Space:** O(n) – to store the counts per y.

---

### 5.6 Code Walkthrough (Java)

```java
public boolean isReflected(int[][] points) {
    // 1️⃣ Find extremes
    int minX = Integer.MAX_VALUE, maxX = Integer.MIN_VALUE;
    for (int[] p : points) {
        minX = Math.min(minX, p[0]);
        maxX = Math.max(maxX, p[0]);
    }
    int center = minX + maxX;      // doubled center

    // 2️⃣ Group by y
    Map<Integer, Map<Integer, Integer>> map = new HashMap<>();
    for (int[] p : points) {
        map.computeIfAbsent(p[1], k -> new HashMap<>())
           .merge(p[0], 1, Integer::sum);
    }

    // 3️⃣ Validate reflection for each y‑group
    for (Map<Integer, Integer> xs : map.values()) {
        for (int x : xs.keySet()) {
            int reflectedX = center - x;
            if (!xs.containsKey(reflectedX) || !xs.get(x).equals(xs.get(reflectedX))) {
                return false;       // mismatch → no reflection
            }
        }
    }
    return true;                    // all pairs matched
}
```

*Notice the use of `center` as a single integer – this avoids floating‑point errors and keeps the algorithm robust even with large coordinates.*

---

### 5.7 Edge Cases & Gotchas

| Edge Case | Why It Matters | Handling |
|-----------|----------------|----------|
| **Single point** | Any vertical line works. | `true` (our algorithm will naturally return `true`). |
| **Duplicate points** | Must match in count. | Count map ensures equal multiplicities. |
| **All points aligned vertically** | Reflection line is the same line. | `center` will equal `2 * same x`; algorithm passes. |
| **Points with negative coordinates** | `minX`/`maxX` handling still works. | No special handling needed. |
| **Large input (10⁴ points)** | Need O(n) algorithm to stay within time limits. | Our solution meets the requirement. |

---

### 5.8 “Good, Bad, Ugly” from a Coding‑Practice View

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Idea** | Using `minX + maxX` to find the reflection center is elegant. | If you forget to double `k`, you may introduce rounding errors. | Trying to brute‑force pairwise reflections (`O(n²)`) will TLE on LeetCode. |
| **Data Structures** | HashMap / Counter for O(1) lookup. | Over‑engineering with custom structs adds noise. | Using sorted vectors but still O(n log n) is acceptable but not optimal. |
| **Implementation** | Clear separation of steps, comments, and integer arithmetic. | Mixing floating‑point arithmetic can lead to bugs. | Not checking multiplicities → wrong answer on duplicates. |
| **Testing** | Unit tests with varied y‑groups and duplicates. | Skipping extreme values (`INT_MAX`) may fail. | Failing to test negative coordinates or single‑point case. |

---

### 5.9 Interview Tips

1. **State the Approach Clearly** – Mention the “center” trick before diving into code.  
2. **Discuss Complexity** – Highlight the O(n) time and space, contrast with brute‑force.  
3. **Ask Clarifying Questions** – Ensure you understand that points can repeat and that the line is vertical.  
4. **Walk Through Edge Cases** – Bring up single point, duplicates, negative coordinates.  
5. **Show a Quick Sample Run** – Manually reflect a small set (e.g., `[[1,1],[-1,1]]`) to demonstrate `center = 0`.  
6. **Explain Why Doubled Center Helps** – Keeps integers, avoids division.  

---

### 5.10 What You Learn

* **Geometric insight → algorithmic efficiency** – Turning a seemingly hard problem into a linear pass.  
* **Hash‑based counting** – A pattern that appears in many interview problems.  
* **Handling duplicates** – A subtle requirement that often trips candidates.  
* **Clean code practice** – Breaking the solution into logical parts, adding comments, and choosing the right data structures.

---

### 5.11 Takeaway

> “Line Reflection” is not just a LeetCode trick question; it’s a micro‑lesson in **problem decomposition, mathematical observation, and efficient data‑structure usage**.  
> By mastering this solution, you’ll be able to tackle other symmetry and reflection problems with confidence—exactly what interviewers look for.

Good luck, and happy coding!

---

## 6. FAQ (for the blog)

| Question | Answer |
|----------|--------|
| *Can the reflection line be horizontal?* | No, the problem explicitly asks for a line parallel to the y‑axis. |
| *What if points are all the same?* | Any vertical line works; algorithm returns `true`. |
| *Is sorting acceptable?* | Yes, an O(n log n) solution that sorts points and checks symmetry works, but the optimal solution is O(n). |
| *Why keep `center` as `minX + maxX`?* | It doubles the actual line position (`k`) but remains an integer, which simplifies checks and avoids floating‑point pitfalls. |

---

*This concludes our deep dive into Line Reflection. Feel free to leave comments or share your own approach!*

--- 

**Happy Interviewing!**