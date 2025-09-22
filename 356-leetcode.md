---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 356. Line Reflection – A Complete Guide  
**(Java + Python + C++ solutions, SEO‑friendly interview blog)**  

---

## TL;DR – The One‑Liner Solution  

> **If all x‑coordinates of the points have the same “mirror center”** (the arithmetic mean of the smallest and largest x‑values), the set is symmetric about the vertical line *x = center*.  
> The trick is to compute the candidate center in **O(n)** time and then verify that every point has a partner reflected across that line using a hash‑set.

```text
center = (minX + maxX) / 2.0
for each point (x, y):
    if (center*2 - x, y) not in set: return false
return true
```

The same logic applies in Java, Python, and C++ – we’ll show the code next.

---

## Why This Blog Matters for Your Interview Prep

* **LeetCode 356 – Line Reflection** is a *medium* difficulty problem that frequently appears in *software‑engineering interviews* (Google, Amazon, Microsoft, etc.).  
* Mastering this problem demonstrates proficiency in:
  * **Hash‑based data structures** (`HashSet`, `unordered_set`, `dict`).
  * **Geometric reasoning** – understanding symmetry and reflection.  
  * **Algorithmic complexity** – an **O(n)** solution beats the naive O(n²).  
* By publishing a **SEO‑optimized article** on Medium, LinkedIn, or your personal blog, you’ll showcase the problem to recruiters, earning you that *“you’re a candidate”* click.

---

## 1. Problem Recap

> **Given `n` 2‑D points (x, y), determine whether there exists a vertical line `x = c` such that reflecting every point across that line leaves the set unchanged.**  
> Duplicate points are allowed.

*Constraints:*  
- `1 ≤ n ≤ 10⁴`  
- `-10⁸ ≤ x, y ≤ 10⁸`

---

## 2. The Key Insight

*Reflecting a point `(x, y)` over a vertical line `x = c` gives `(2c - x, y)`.*  
Therefore:

*All points must be pairable: for every `(x, y)` there must exist `(2c - x, y)` in the set.*

If we can find the **unique** value of `c`, we can test the whole set in linear time.

### How to find `c`?

Consider the **smallest** and **largest** x‑coordinates among all points, say `minX` and `maxX`.  
Any vertical symmetry line must lie exactly halfway between them; otherwise one side would extend further than the other.

Thus:

```
c = (minX + maxX) / 2.0
```

*Proof sketch:*  
If a symmetry line exists, every point on the left side must have a mirror on the right side. The farthest left point (`minX`) reflects to the farthest right point (`maxX`). Their midpoint must be the reflection line.

---

## 3. Algorithms

| Language | Complexity | Space |
|----------|------------|-------|
| Java     | O(n)       | O(n)  |
| Python   | O(n)       | O(n)  |
| C++      | O(n)       | O(n)  |

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public boolean isReflected(int[][] points) {
        if (points == null || points.length == 0) return true;

        double minX = Double.MAX_VALUE, maxX = Double.MIN_VALUE;
        Set<Long> set = new HashSet<>();

        for (int[] p : points) {
            int x = p[0], y = p[1];
            minX = Math.min(minX, x);
            maxX = Math.max(maxX, x);
            set.add(hash(x, y));
        }

        double center = (minX + maxX) / 2.0;

        for (int[] p : points) {
            int x = p[0], y = p[1];
            int reflectedX = (int) (2 * center - x);
            if (!set.contains(hash(reflectedX, y))) {
                return false;
            }
        }
        return true;
    }

    // Convert a pair to a unique long key
    private long hash(int x, int y) {
        return (((long) x) << 32) ^ (y & 0xffffffffL);
    }
}
```

### 3.2 Python

```python
class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        if not points:
            return True

        xs = [p[0] for p in points]
        min_x, max_x = min(xs), max(xs)
        center = (min_x + max_x) / 2.0

        point_set = {tuple(p) for p in points}

        for x, y in points:
            reflected = (int(round(2 * center - x)), y)
            if reflected not in point_set:
                return False
        return True
```

> *Why `round`?*  
> Because `2 * center` may produce a floating point like `2.0` when `center` is `1.0`. Casting to `int` after rounding guarantees the exact integer reflection.

### 3.3 C++

```cpp
class Solution {
public:
    bool isReflected(vector<vector<int>>& points) {
        if (points.empty()) return true;

        int minX = INT_MAX, maxX = INT_MIN;
        unordered_set<long long> s;

        for (auto& p : points) {
            int x = p[0], y = p[1];
            minX = min(minX, x);
            maxX = max(maxX, x);
            s.insert(((long long)x << 32) ^ (y & 0xffffffffLL));
        }

        double center = (minX + maxX) / 2.0;

        for (auto& p : points) {
            int x = p[0], y = p[1];
            int reflectedX = (int)round(2 * center - x);
            long long key = ((long long)reflectedX << 32) ^ (y & 0xffffffffLL);
            if (!s.count(key)) return false;
        }
        return true;
    }
};
```

---

## 4. Edge‑Case Handling

| Edge case | Why it matters | Handling |
|-----------|----------------|----------|
| Duplicate points | Two identical points are their own mirrors. | The hash‑set stores unique keys; duplicates don’t disturb logic. |
| Odd number of points | Still possible if a point lies exactly on the reflection line. | The algorithm handles it automatically: the reflected point equals itself. |
| Extremely large coordinates | Prevent integer overflow when computing `minX + maxX`. | Use `double` for the center; cast back to `int` only after rounding. |
| Negative coordinates | Symmetry works the same regardless of sign. | No special handling needed. |

---

## 5. Complexity Analysis

* **Time**:  
  *Computing min/max* → O(n)  
  *Building the set* → O(n)  
  *Verification loop* → O(n)  
  **Total**: **O(n)**

* **Space**:  
  The hash‑set stores each unique point → **O(n)**

This is the optimal solution; any algorithm that examines each point at least once must be linear.

---

## 6. The Good, The Bad, and The Ugly

### Good  
* **Elegant math insight** – the center is simply `(minX + maxX)/2`.  
* **Simple implementation** – a handful of lines in any language.  
* **Robustness** – handles duplicates, negative numbers, and large coordinates.  
* **Interview friendly** – demonstrates clear thinking and hash‑set usage.

### Bad  
* **Potential pitfalls** – off‑by‑one errors when converting to integers.  
* **Floating‑point quirks** – `center` is a double; if you blindly cast to int you may miss a valid mirror.  
* **Large input handling** – not a problem in Java/Python/C++ but some languages (e.g., JavaScript) may struggle with 10⁴ points in a tight memory budget.

### Ugly  
* **People over‑optimize** – writing a O(n²) nested loop or a sort‑then‑two‑pointer approach with extra bookkeeping.  
* **Ignoring duplicates** – naive set logic that fails when the same point appears twice.  
* **Using hash‑maps with complex keys** – over‑engineering by defining a `Point` class with `equals()`/`hashCode()`.

---

## 7. SEO‑Optimized Blog Structure

1. **Title**  
   `Line Reflection – LeetCode 356 (Java, Python, C++) – O(n) Solution Explained`

2. **Meta Description**  
   `Solve LeetCode 356 – Line Reflection in Java, Python, and C++ with a linear‑time algorithm. Learn the math trick, see full code, and ace your next software‑engineering interview.`

3. **Headings (H1, H2, H3)**  
   * H1: `Line Reflection – LeetCode 356`  
   * H2: `Problem Statement`  
   * H2: `Key Insight & Mathematical Trick`  
   * H2: `Algorithm & Pseudocode`  
   * H3: `Java Implementation`  
   * H3: `Python Implementation`  
   * H3: `C++ Implementation`  
   * H2: `Complexity Analysis`  
   * H2: `Edge‑Case Handling`  
   * H2: `The Good, The Bad, and The Ugly`  
   * H2: `Why This Matters for Interviews`  

4. **Keywords**  
   `LeetCode 356`, `Line Reflection`, `vertical line symmetry`, `O(n) solution`, `hash set`, `software engineering interview`, `Java`, `Python`, `C++`, `algorithm interview questions`, `job interview coding`.

5. **Internal/External Links**  
   * Link to the LeetCode problem page.  
   * Link to other interview prep articles on your blog.  
   * Optionally, link to GitHub repository with all solutions.

6. **Code Blocks**  
   Use syntax highlighting (````java`, ````python`, ````cpp`).  
   Add explanatory comments inline.

7. **Images / Diagrams**  
   * A simple diagram showing a point and its reflection.  
   * A table comparing runtimes.

8. **Call‑to‑Action**  
   `Want to master more LeetCode problems? Subscribe to our newsletter!`

---

## 8. Sample Blog Excerpt (to copy‑paste)

> ### Problem Recap
> 
> *Given a list of 2‑D points, can you find a vertical line such that reflecting every point across that line yields exactly the same set of points?*
> 
> The challenge is to decide this in linear time.

> ### The One‑Liner Insight
> 
> Compute `minX` and `maxX`. The symmetry line must be at `center = (minX + maxX) / 2`.  
> Then verify that for each `(x, y)` the point `(2*center - x, y)` exists.

> ### Java Implementation
> 
> ```java
> public boolean isReflected(int[][] points) { … }
> ```
> 
> *Full code in the article.*

> ### Why It’s Interview‑Ready
> 
> * O(n) time, O(n) space – optimal.  
> * Uses hash‑set – common interview data structure.  
> * Handles duplicates & negative coordinates.  

> **Got stuck on LeetCode 356?**  
> Drop a comment below or email me – happy to help you get that interview call!

---

## 9. Next Steps for Your Career

1. **Push the code to GitHub** – tag the repo with `#LeetCode356` and link it in your LinkedIn.  
2. **Add the blog to your portfolio** – recruiters love a clean, readable explanation.  
3. **Practice variations** – e.g., reflection over a horizontal line, or reflection over a general line `ax + by + c = 0`.  
4. **Mock Interviews** – use platforms like Pramp or Interviewing.io; reference the solution when asked.  
5. **Keep learning** – next LeetCode problem: *378. Sum and Product of Array Elements* (O(n) with a hashmap).

---

## 10. Final Takeaway

> The **Line Reflection** problem is a shining example of how a little geometry can turn a potentially quadratic algorithm into a clean, linear‑time solution. Master it, publish the explanation, and you’ll not only ace this LeetCode problem but also demonstrate to recruiters that you can spot patterns, write efficient code, and communicate clearly – the perfect recipe for landing your dream software‑engineering role.  

Happy coding, and good luck on your next interview! 🚀