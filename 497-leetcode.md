---
title: LeetCode 497. Random Point in Non-overlapping Rectangles - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code

Below are clean, self‑contained implementations for **Java**, **Python** and **C++** that solve LeetCode 497 – *Random Point in Non‑overlapping Rectangles*.  
All three versions use the same idea:

| Step | Idea |
|------|------|
| 1 | Pre‑compute the **number of integer points** in each rectangle:  
`area = (x2 – x1 + 1) * (y2 – y1 + 1)` |
| 2 | Build a **prefix‑sum array** (`pointsPrefix`) so that `pointsPrefix[i]` is the total number of points in rectangles `0 … i`. |
| 3 | Pick a random integer `t` in `[0, totalPoints – 1]`. |
| 4 | Binary‑search `pointsPrefix` to find the rectangle that contains index `t`. |
| 5 | Convert the local index inside that rectangle into `x` and `y` coordinates. |

The resulting point is **uniformly distributed** over all integer points of all rectangles.

---

### Java (Java 17)

```java
import java.util.*;

class Solution {
    private final int[][] rects;
    private final int[] prefix;      // prefix sum of point counts
    private final Random rand = new Random();

    public Solution(int[][] rects) {
        this.rects = rects;
        int n = rects.length;
        prefix = new int[n];
        int total = 0;
        for (int i = 0; i < n; i++) {
            int[] r = rects[i];
            int pts = (r[2] - r[0] + 1) * (r[3] - r[1] + 1);
            total += pts;
            prefix[i] = total;
        }
    }

    public int[] pick() {
        int target = rand.nextInt(prefix[prefix.length - 1]); // [0, total-1]
        int rectIdx = binarySearch(target);
        int[] r = rects[rectIdx];
        int width  = r[2] - r[0] + 1;
        int height = r[3] - r[1] + 1;
        int base = rectIdx == 0 ? 0 : prefix[rectIdx - 1];
        int offset = target - base;          // 0‑based offset inside this rectangle
        int x = r[0] + (offset % width);
        int y = r[1] + (offset / width);
        return new int[]{x, y};
    }

    private int binarySearch(int target) {
        int lo = 0, hi = prefix.length - 1;
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (target < prefix[mid]) hi = mid;
            else lo = mid + 1;
        }
        return lo;
    }
}
```

---

### Python (Python 3.10)

```python
import random
import bisect
from typing import List

class Solution:
    def __init__(self, rects: List[List[int]]):
        self.rects = rects
        self.prefix = []
        total = 0
        for r in rects:
            pts = (r[2] - r[0] + 1) * (r[3] - r[1] + 1)
            total += pts
            self.prefix.append(total)

    def pick(self) -> List[int]:
        target = random.randint(0, self.prefix[-1] - 1)   # inclusive
        rect_idx = bisect.bisect_left(self.prefix, target)
        r = self.rects[rect_idx]
        width = r[2] - r[0] + 1
        height = r[3] - r[1] + 1
        base = 0 if rect_idx == 0 else self.prefix[rect_idx - 1]
        offset = target - base
        x = r[0] + offset % width
        y = r[1] + offset // width
        return [x, y]
```

---

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> rects;
    vector<long long> prefix;          // use long long for safety
    mt19937 rng{random_device{}()};

    Solution(vector<vector<int>>& rects_) : rects(rects_) {
        long long total = 0;
        for (auto& r : rects) {
            long long pts = 1LL * (r[2] - r[0] + 1) * (r[3] - r[1] + 1);
            total += pts;
            prefix.push_back(total);
        }
    }

    vector<int> pick() {
        uniform_int_distribution<long long> dist(0, prefix.back() - 1);
        long long target = dist(rng);               // [0, total-1]
        int rectIdx = lower_bound(prefix.begin(), prefix.end(), target) - prefix.begin();

        const auto& r = rects[rectIdx];
        long long width = r[2] - r[0] + 1;
        long long height = r[3] - r[1] + 1;
        long long base = rectIdx == 0 ? 0 : prefix[rectIdx - 1];
        long long offset = target - base;           // 0‑based inside rectangle

        int x = r[0] + int(offset % width);
        int y = r[1] + int(offset / width);
        return {x, y};
    }
};
```

All three solutions run in **O(log n)** time per `pick()` (binary search on at most 100 rectangles) and **O(n)** time for initialization.

---

## 2.  Blog Article

> **Title**  
> “LeetCode 497 – Random Point in Non‑overlapping Rectangles: Java, Python & C++ Solutions (The Good, The Bad, & The Ugly)”

---

### Introduction

If you’re preparing for a coding interview, you’ll quickly find that **randomization** can be a surprisingly tricky topic. LeetCode’s *Random Point in Non‑overlapping Rectangles* (Problem 497) is a classic example that tests not only your data‑structures knowledge but also your ability to reason about probability and efficiency.

In this article we’ll:

1.  Recap the problem statement.  
2.  Discuss the pitfalls that can turn a great idea into a *bug‑prone* implementation.  
3.  Present a clean, **O(n)** pre‑processing + **O(log n)** query solution.  
4.  Show code in **Java, Python, and C++**.  
5.  Explore “the good, the bad, and the ugly” aspects of the problem and its solutions.  
6.  Offer interview‑ready insights and potential follow‑up questions.

Let’s dive in.

---

### 1. Problem Restatement

You’re given an array `rects` of non‑overlapping, axis‑aligned rectangles.  
Each rectangle is `[x1, y1, x2, y2]` where `(x1, y1)` is the bottom‑left corner and `(x2, y2)` the top‑right corner (both inclusive).

> **Task** – Design a class that:
> * Initializes with `rects`.  
> * Returns a random integer point `[x, y]` that lies inside *any* of the rectangles, with each integer point being equally likely.

Constraints:  
* `1 ≤ rects.length ≤ 100`  
* `-10^9 ≤ x1 < x2, y1 < y2 ≤ 10^9`  
* `x2 – x1 ≤ 2000`, `y2 – y1 ≤ 2000`  
* All rectangles are disjoint.  
* Up to `10^5` calls to `pick()` in a test case.

---

### 2. The Pitfalls – *Why Randomization is Hard*

| Pitfall | Why it matters | Typical “gotcha” |
|---------|----------------|------------------|
| **Inclusive vs. exclusive bounds** | Many people forget that the rectangle corners are *inclusive*. If you treat them as exclusive, you’ll under‑count points. | `(x2 - x1) * (y2 - y1)` instead of `(x2 - x1 + 1) * (y2 - y1 + 1)` |
| **Overflow** | `x2 – x1 + 1` can be up to 2001. Squared gives ~4 M. 100 rectangles → ~400 M points. Still fits in `int`, but using a 32‑bit signed integer can be unsafe in some languages (e.g., Java’s `int` is 32‑bit, but `long` is safer). | Using `int` everywhere in Java may lead to subtle bugs if test data pushes the upper bound. |
| **Random range off‑by‑one** | `Random.nextInt(n)` returns `[0, n‑1]`. Forgetting the inclusive end leads to a **biased** distribution. | Mixing `nextInt(n)` with `nextInt(n) + 1` or `randrange(n+1)` incorrectly. |
| **Linear search per query** | With up to 100 rectangles, a linear search is fine, but it’s still *O(n)* per `pick()`. In an interview you’ll want to show a logarithmic step (binary search) if you can. | Not using `bisect`/`lower_bound`, or scanning every rectangle for every pick. |
| **State mutation** | The class must maintain the prefix sums; if you recompute them every call, you’ll lose the O(1) advantage. | Re‑building prefix sums inside `pick()` → TLE on heavy test cases. |

---

### 3. The Elegant Solution

**Core idea** –  Think of all integer points as a *single linear array* of length `totalPoints`.  
If we could map a random index in this array to a concrete `(x, y)` point, we’d have a uniform distribution automatically.

1. **Point Count per Rectangle**  
   Each rectangle contains  
   ```text
   pts = (x2 - x1 + 1) * (y2 - y1 + 1)
   ```  
   (the `+1` makes the corners inclusive).

2. **Prefix Sum Array** – `pointsPrefix[i]` = total number of points in rectangles `0 … i`.  
   *Build once in O(n).*

3. **Random Target** – pick an integer `t` in `[0, totalPoints-1]`.  
   Every value of `t` corresponds to one unique point in the global linear array.

4. **Binary Search** – find the smallest `i` such that `pointsPrefix[i] > t`.  
   That rectangle contains the point with local offset `t - previousPrefix`.

5. **Translate Offset → Coordinates**  
   Inside the chosen rectangle, `width = x2 - x1 + 1`.  
   ```text
   localX = x1 + (offset % width)
   localY = y1 + (offset / width)
   ```

Because each integer point has an *identical* representation in the linear array, the resulting point is uniformly random.

> **Why binary search?**  
> `pointsPrefix` is sorted (monotonically increasing). A standard `lower_bound`/`bisect_left` gives us the rectangle in **O(log n)** time – well within the problem’s limits (100 rectangles) and interview expectations.

---

### 4. Code Walkthrough (Java)

```java
public class Solution {
    private final int[][] rects;
    private final int[] prefix;
    private final Random rand = new Random();

    public Solution(int[][] rects) {
        this.rects = rects;
        int n = rects.length;
        prefix = new int[n];
        int total = 0;
        for (int i = 0; i < n; i++) {
            int[] r = rects[i];
            int pts = (r[2] - r[0] + 1) * (r[3] - r[1] + 1);
            total += pts;
            prefix[i] = total;
        }
    }

    public int[] pick() {
        int target = rand.nextInt(prefix[prefix.length - 1]); // [0,total-1]
        int idx = binarySearch(target);
        int[] r = rects[idx];
        int width = r[2] - r[0] + 1;
        int height = r[3] - r[1] + 1;
        int base = idx == 0 ? 0 : prefix[idx - 1];
        int offset = target - base;
        int x = r[0] + (offset % width);
        int y = r[1] + (offset / width);
        return new int[]{x, y};
    }

    private int binarySearch(int target) {
        int lo = 0, hi = prefix.length - 1;
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (target < prefix[mid]) hi = mid;
            else lo = mid + 1;
        }
        return lo;
    }
}
```

**Take‑away** – the class is stateless after construction; `pick()` is pure and fast.

---

### 5. “The Good, the Bad, and the Ugly”

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Algorithmic elegance** | Using a prefix sum + binary search is a textbook “transform‑then‑search” pattern. | Failing to treat bounds inclusively leads to biased results. | Trying to use a naive “generate random point in bounding box and reject” approach results in exponential time when rectangles are tiny or sparse. |
| **Randomness correctness** | `nextInt(total)` + offset conversion guarantees uniformity without post‑processing. | Mis‑calculating the offset (e.g., forgetting the `+1` on width) biases the distribution. | Returning a point based on `Math.random()` (float) and then rounding can introduce hidden bias because the float resolution is not uniform over the integer range. |
| **Performance** | Pre‑processing is linear in `n` (≤ 100). Query is logarithmic. | Using a linear search per `pick()` is still acceptable here, but an interview might expect `O(log n)` if `n` can grow. | Generating random numbers in the 64‑bit space without a proper modulus can overflow or under‑sample. |
| **Language quirks** | Java’s `Random.nextInt()` is **inclusive‑exclusive** – be careful with off‑by‑one. | Python’s `random.randint(a, b)` is inclusive on both ends; adjust accordingly. | C++’s `uniform_int_distribution` takes **inclusive bounds** – use `dist(rng)` properly. |
| **Potential interview traps** | Asking “What if the rectangles overlapped?” – you’d need a different approach (e.g., weighted random intervals). | “What if the coordinates were floating‑point?” – the same idea works, but you’d need to pick from a continuous range. | “What if we had to support dynamic insert/delete of rectangles?” – you’d need a segment tree or Fenwick tree. |

---

### 6. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| **Construction** | `O(n)` – single pass to build prefix sums. | `O(n)` – store the rectangles and prefix array. |
| **pick()** | `O(log n)` – binary search on the prefix array (`n ≤ 100`). | `O(1)` – constant extra memory. |

Because `n` is bounded by 100, this is essentially *constant* time per query in practice, but the asymptotic guarantees make the solution robust for larger inputs.

---

### 7. Interview‑Ready Insights

* **Why did we add `+1` to width & height?**  
  The rectangle coordinates are inclusive. Counting the points along one axis must include both endpoints.

* **What if rectangles were allowed to overlap?**  
  The prefix sum technique still works, but you must ensure you’re not double‑counting overlapping regions. One common trick is to **scanline** the plane to build a set of disjoint sub‑rectangles first.

* **What if we needed *continuous* random points?**  
  Replace the integer offset calculation with a uniformly generated floating point in `[0, width)` and `[0, height)`. The binary search logic stays the same.

* **Could we do better?**  
  For a very large `n`, you could use a **segment tree** or **Fenwick tree** to achieve `O(log n)` pre‑processing and `O(log n)` queries. But with `n ≤ 100` the simple array is preferable for its clarity.

---

### 8. Follow‑Up Questions for the Interviewer

1. **Memory constraints** – *What if we had 10^5 rectangles?*  
   Discuss how the prefix array scales and whether you’d need a more sophisticated data‑structure (Fenwick/segment tree).

2. **Dynamic updates** – *Can rectangles be inserted or removed after construction?*  
   Talk about balancing update vs. query performance.

3. **Error handling** – *What if the random generator is weak or biased?*  
   Emphasize using proven library functions and avoiding custom PRNGs unless specified.

4. **Edge cases** – *What happens when a rectangle has zero area?*  
   The point count becomes 0; the algorithm must handle this gracefully (skip such rectangles).

5. **Precision** – *Suppose the coordinates are real numbers with up to 6 decimal places.*  
   Show how to adapt the offset calculation to floating points while preserving uniformity.

---

### 9. Summary

- The problem is a classic *mapping random index → point* question.  
- A prefix sum of point counts + binary search yields an **exactly uniform** solution.  
- Be careful with inclusivity, overflow, and random range boundaries.  
- The presented Java code, along with its Python/C++ counterparts, showcases clean, efficient, and language‑aware implementation.

Good luck with your interview – you now have both the algorithmic insight and the code to back it up!