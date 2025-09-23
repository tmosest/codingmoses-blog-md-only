---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 356. Line Reflection – A Deep‑Dive Guide (Java / Python / C++)

> **Problem**:  
> You are given an array of points on a 2‑D plane.  
> Determine whether there exists a vertical line (parallel to the *y*‑axis) that reflects all points onto each other.  
> In other words, after reflecting every point across that line, the set of points remains unchanged (duplicates are allowed).

---

### 1.  Why This Problem is a Great Interview Topic

| Category | Why it matters in a job interview |
|----------|------------------------------------|
| **Mathematics** | Requires a clear understanding of symmetry, mid‑points, and arithmetic. |
| **Data Structures** | Demonstrates knowledge of hash‑based sets/maps. |
| **Algorithmic Thinking** | Encourages “O(n)” solutions vs. brute‑force “O(n²)”. |
| **Edge Cases** | Handles large coordinates, duplicate points, negative numbers. |
| **Language‑agnostic** | Perfect for showcasing your favorite language (Java, Python, C++). |

If you can walk through the solution while highlighting your reasoning, you’ll impress hiring managers who care about problem‑solving, not just code.

---

## 2.  Intuition & Observation

1. **Mid‑point of a pair**:  
   If two points *p1* = (x1, y) and *p2* = (x2, y) lie on the same horizontal line (*y* is identical), then a vertical reflection line that maps *p1* ↔ *p2* must be located at  
   \[
   x_{\text{mid}} = \frac{x_1 + x_2}{2}
   \]
   i.e. the average of their x‑coordinates.

2. **All pairs must share the same mid‑point**:  
   For a single line of reflection to exist, every point must pair with another point (or itself) such that all mid‑points are identical.  

3. **Duplicates**:  
   A point may reflect onto itself if its *x* equals the mid‑point. Multiple identical points pose no problem; they all “reflected” onto the same spot.

4. **No pair found**:  
   If the set of points is odd and no point has its own reflection (i.e., `x == mid`), the answer is automatically **false**.

---

## 3.  Algorithm

1. **Compute the theoretical reflection line**  
   *Take the first two points that share the same y‑coordinate.*  
   The line will be at  
   \[
   \text{mid} = \frac{x_1 + x_2}{2}
   \]  
   This *mid* is a rational number; we’ll store it as a double or use integer arithmetic by storing `sum = x_1 + x_2`.

2. **Build a set of points**  
   Represent each point as a pair `(x, y)` (e.g., as a string `"x#y"` or a custom object that implements `hashCode`/`equals`).

3. **Validate every point**  
   For each point `(x, y)`  
   *Compute its counterpart’s x‑coordinate*  
   \[
   x_{\text{mirror}} = 2 \times \text{mid} - x
   \]  
   *Check if `(x_{\text{mirror}}, y)` exists in the set.*  
   If any point fails this test → return `false`.

4. **All passed** → return `true`.

### Edge‑case handling

| Situation | What to do |
|-----------|------------|
| No two points share a y | Use the first point alone: its reflection is itself → always true (vertical line at its x). |
| Odd number of points, no self‑reflection | Immediately false. |
| Extremely large coordinates | Use `long` or double carefully; integer overflow is avoided by computing `mid` as `long midSum = x1 + x2;` and then using `midSum - x`. |

---

## 4.  Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Building the set | **O(n)** | **O(n)** |
| Validating points | **O(n)** | – |
| **Total** | **O(n)** | **O(n)** |

The solution is linear, which is the optimum for this problem.

---

## 5.  Code Implementations

Below are clean, commented implementations in **Java**, **Python**, and **C++**.  
Each uses a hash‑based set to store points, so the look‑up is amortized O(1).

---

### 5.1 Java

```java
import java.util.*;

public class Solution {
    // O(n) time, O(n) space
    public boolean isReflected(int[][] points) {
        if (points == null || points.length == 0) return true;

        // Use a HashSet of encoded points "x#y"
        Set<String> set = new HashSet<>();
        for (int[] p : points) {
            set.add(p[0] + "#" + p[1]);
        }

        // Find a candidate line: sum of two x's of any two points with the same y
        Long midSum = null;          // stores 2*mid as integer (x1 + x2)
        for (int[] p : points) {
            if (midSum == null) {    // initialise with the first point
                midSum = (long) p[0] * 2; // 2 * x1 (so far)
            }
            for (int[] q : points) {
                if (p != q && p[1] == q[1]) {
                    midSum = (long) p[0] + q[0];
                    break;
                }
            }
            if (midSum != null) break;
        }

        // If all y's are different, any line works: we just need to check pairs.
        // We'll treat midSum as 2*mid, which might be null. In that case,
        // we set midSum to sum of the first point's x with itself.
        if (midSum == null) {
            midSum = (long) points[0][0] * 2;
        }

        for (int[] p : points) {
            long mirroredX = midSum - p[0]; // 2*mid - x
            String mirrorKey = mirroredX + "#" + p[1];
            if (!set.contains(mirrorKey)) {
                return false;
            }
        }
        return true;
    }
}
```

> **Why this works**  
> `midSum` is the *sum* of the two x‑coordinates that determine the reflection line.  
> The mirror of `x` across that line is `midSum - x`.  
> Because we store every point in a set, each check is O(1).

---

### 5.2 Python

```python
class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        if not points:
            return True

        # Set of tuples for O(1) look‑ups
        point_set = {tuple(p) for p in points}

        # Determine 2*mid from any pair of points on the same horizontal line
        mid_sum = None
        for i, (x1, y1) in enumerate(points):
            for j, (x2, y2) in enumerate(points):
                if i != j and y1 == y2:
                    mid_sum = x1 + x2
                    break
            if mid_sum is not None:
                break

        # If no pair found (all y's different), mid_sum will be 2 * first x
        if mid_sum is None:
            mid_sum = points[0][0] * 2

        for x, y in points:
            mirrored_x = mid_sum - x
            if (mirrored_x, y) not in point_set:
                return False
        return True
```

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isReflected(vector<vector<int>>& points) {
        if (points.empty()) return true;

        // Hash set of encoded points
        unordered_set<long long> set;
        for (auto &p : points) {
            long long key = ((long long)p[0] << 32) ^ (p[1] & 0xffffffff);
            set.insert(key);
        }

        // Find 2*mid (sum of x1 and x2) from any pair on same y
        long long midSum = LLONG_MAX;
        bool found = false;
        for (size_t i = 0; i < points.size() && !found; ++i) {
            for (size_t j = i + 1; j < points.size(); ++j) {
                if (points[i][1] == points[j][1]) {
                    midSum = (long long)points[i][0] + points[j][0];
                    found = true;
                    break;
                }
            }
        }

        if (!found) {                     // all y's unique
            midSum = (long long)points[0][0] * 2;
        }

        for (auto &p : points) {
            long long mirroredX = midSum - p[0];
            long long key = (mirroredX << 32) ^ (p[1] & 0xffffffff);
            if (!set.count(key)) return false;
        }
        return true;
    }
};
```

---

## 6.  Testing & Edge Cases

| Test | Input | Expected | Why |
|------|-------|----------|-----|
| 1 | [[1,1],[-1,1]] | true | Mid at 0 |
| 2 | [[1,1],[-1,-1]] | false | Different y |
| 3 | [[2,3], [2,3]] | true | Duplicate points, line at x=2 |
| 4 | [[-5,0],[5,0],[0,0]] | true | Mid at 0, includes self‑reflection |
| 5 | [[0,1],[2,1],[4,1]] | false | Odd number, no point at mid 2 |
| 6 | [[-1e8,1e8],[1e8,1e8]] | true | Large coordinates, mid 0 |

Run these through your IDE or LeetCode’s “Run Testcases” to verify.

---

## 7.  The Good, The Bad, and The Ugly

### The Good

- **Linear time** – O(n), satisfying the “follow‑up” requirement.
- **Simple math** – Only one mid‑point calculation.
- **Versatile** – Works for duplicate points, negative coordinates, and any n.
- **Language‑agnostic** – Implementation patterns are identical across Java, Python, C++.

### The Bad

- **Potential integer overflow** in languages with 32‑bit `int`.  
  *Solution*: use 64‑bit (`long`/`long long`) for sums.
- **Hash collisions**: With very large data sets, a custom hash (e.g., encoding pair into a 64‑bit integer) can avoid slowness.

### The Ugly

- **Choosing the wrong “midSum”**: If you accidentally pick two points with different *y*’s, the algorithm fails.  
  *Fix*: ensure the pair shares the same y‑coordinate or fallback to default line (double first x).

---

## 8.  Interview‑Ready Tips

1. **Start with a clear explanation**:  
   *“I’m going to look for a vertical line that acts as a mirror. That means every point’s mirror has to exist in the set.”*

2. **Talk through edge cases**:  
   *“If all y’s are unique, we can still choose any line. I’ll simply pair a point with itself.”*

3. **State complexity explicitly**:  
   *“Set creation O(n), lookup O(1), total O(n). Space O(n).”*

4. **Mention overflow handling**:  
   *“I’ll use 64‑bit sums so 2×10⁸ + 2×10⁸ still fits.”*

5. **Show a small example on the whiteboard**:  
   *Draw points and the mid‑point line.*

6. **Ask clarifying questions**:  
   *“Do you consider duplicate points as separate?” – yes, but they don’t affect logic.*

---

## 9.  SEO‑Optimized Blog Post Summary

> **Title**: *Line Reflection Problem (LeetCode 356) – Java, Python, C++ Solutions & Interview Tips*  
> **Meta Description**:  
> Master LeetCode 356, “Line Reflection,” with efficient O(n) solutions in Java, Python, and C++. Learn the math, edge cases, and interview‑ready explanations to land your next software engineering job.  
> **Keywords**: LeetCode 356, line reflection, vertical symmetry, hash set solution, interview problem, Java coding interview, Python algorithm, C++ coding challenge, software engineer interview tips, algorithmic problem solving, data structure interview questions.

> **Why read this article?**  
> * Build confidence in solving symmetry problems.  
> * Showcase your understanding of hash‑based data structures.  
> * Learn how to turn a coding problem into a conversation‑ready interview story.

--- 

### Closing Thought

Symmetry problems like Line Reflection test a candidate’s ability to see patterns, reduce a 2‑D geometric problem to a simple arithmetic check, and use the right data structure for constant‑time look‑ups. Mastering this solution demonstrates you can translate a real‑world requirement (mirror‑like reflection) into clean, production‑ready code—a key skill for any software engineer. Good luck landing that job! 🚀