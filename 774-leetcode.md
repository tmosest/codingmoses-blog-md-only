---
title: LeetCode 774. Minimize Max Distance to Gas Station - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 774. Minimize Max Distance to Gas Station  
### Java | Python | C++ – Full Solutions  
### SEO‑Optimized Blog Post: “The Good, The Bad, & The Ugly of LeetCode 774”

---

### 📌 Problem Summary  
You’re given an **array of gas‑station coordinates** on the x‑axis (`stations`, strictly increasing) and an integer `k` – the number of new stations you can add anywhere (not necessarily at integer positions).  
Your goal is to minimize the **maximum distance** between any two consecutive stations after adding exactly `k` new ones.  

Return that minimal possible maximum distance (`penalty`). Answers within `10⁻⁶` are accepted.

> *Examples*  
> - `stations = [1,2,3,4,5,6,7,8,9,10], k = 9 → 0.5`  
> - `stations = [23,24,36,39,46,56,57,65,84,98], k = 1 → 14.0`

**Constraints**

| # | Constraint | Explanation |
|---|------------|-------------|
| 1 | `10 <= stations.length <= 2000` | Small enough for O(n log) |
| 2 | `0 <= stations[i] <= 10⁸` | Large coordinate values |
| 3 | `1 <= k <= 10⁶` | Up to a million new stations |

---

## 🎯 Core Insight  

We can **binary‑search** over the answer.  
For a guessed `mid` (max allowed gap), check if we can fill every original gap with at most `k` new stations so that each sub‑gap ≤ `mid`.

*If we can*, try a smaller `mid`.  
*If we can’t*, we need a larger `mid`.

This gives us `O(n log(maxGap))` time, `O(1)` extra space.

### Helper: `stationsNeeded(gap, mid)`  
Number of new stations needed to split a gap of length `gap` into segments of size ≤ `mid`:

```
required = ceil(gap / mid) - 1
```

The `-1` comes from the fact that a gap of length `mid` already needs 0 new stations.

---

## 📚 Code Implementations  

Below are clean, well‑commented solutions in **Java, Python, and C++**. Each solution follows the same logic and has the same time & space complexity.

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    public double minmaxGasDist(int[] stations, int k) {
        // Upper bound: the largest original gap
        double maxGap = 0;
        for (int i = 1; i < stations.length; i++) {
            maxGap = Math.max(maxGap, stations[i] - stations[i-1]);
        }

        double left = 0, right = maxGap;
        final double EPS = 1e-6;          // precision tolerance

        while (right - left > EPS) {
            double mid = (left + right) / 2.0;
            if (canAchieve(stations, k, mid)) {
                right = mid;              // try a smaller max gap
            } else {
                left = mid;               // need a larger gap
            }
        }
        return left;
    }

    /** Greedy check: is it possible to keep all gaps <= penalty? */
    private boolean canAchieve(int[] stations, int k, double penalty) {
        int needed = 0;
        for (int i = 1; i < stations.length; i++) {
            double gap = stations[i] - stations[i-1];
            // ceil(gap / penalty) - 1 stations needed
            needed += (int) Math.ceil(gap / penalty) - 1;
            if (needed > k) return false;   // early exit
        }
        return true;
    }
}
```

---

### 2️⃣ Python

```python
from typing import List
import math

class Solution:
    def minmaxGasDist(self, stations: List[int], k: int) -> float:
        # Upper bound: max existing gap
        max_gap = max(stations[i] - stations[i-1] for i in range(1, len(stations)))

        left, right = 0.0, float(max_gap)
        eps = 1e-6

        while right - left > eps:
            mid = (left + right) / 2.0
            if self.can_achieve(stations, k, mid):
                right = mid
            else:
                left = mid
        return left

    def can_achieve(self, stations: List[int], k: int, penalty: float) -> bool:
        needed = 0
        for i in range(1, len(stations)):
            gap = stations[i] - stations[i-1]
            needed += math.ceil(gap / penalty) - 1
            if needed > k:
                return False
        return True
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    double minmaxGasDist(vector<int>& stations, int k) {
        double maxGap = 0;
        for (size_t i = 1; i < stations.size(); ++i)
            maxGap = max(maxGap, static_cast<double>(stations[i] - stations[i-1]));

        double left = 0, right = maxGap;
        const double EPS = 1e-6;

        while (right - left > EPS) {
            double mid = (left + right) / 2.0;
            if (canAchieve(stations, k, mid))
                right = mid;          // try smaller
            else
                left = mid;           // need bigger
        }
        return left;
    }

private:
    bool canAchieve(const vector<int>& stations, int k, double penalty) {
        long long needed = 0;
        for (size_t i = 1; i < stations.size(); ++i) {
            double gap = stations[i] - stations[i-1];
            needed += static_cast<long long>(ceil(gap / penalty)) - 1;
            if (needed > k) return false;  // early exit
        }
        return true;
    }
};
```

---

## 📈 Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java     | `O(n log(maxGap))` | `O(1)` |
| Python   | `O(n log(maxGap))` | `O(1)` |
| C++      | `O(n log(maxGap))` | `O(1)` |

*`n = stations.length`, `maxGap` ≤ 10⁸.*

---

## 📝 Blog Article – “The Good, The Bad, & The Ugly of LeetCode 774”

### Meta Description  
> Master LeetCode 774 – *Minimize Max Distance to Gas Station*. Learn the binary‑search trick, see clean Java/Python/C++ solutions, and understand the algorithmic nuances that interviewers love. Perfect your coding interview prep!

---

## 📖 Introduction

If you’re prepping for a software‑engineering interview, you’ve probably stumbled across **LeetCode 774**: *Minimize Max Distance to Gas Station*.  
It’s a classic **greedy + binary search** problem that tests:

- **Binary search on answer** (a powerful technique for continuous domains).  
- **Greedy counting** of inserted stations.  
- Careful handling of **floating‑point precision**.

Below we dissect the problem, walk through the optimal solution, and show implementations in Java, Python, and C++. Finally, we reflect on the “good, bad, ugly” aspects that will help you ace interviews and land that dream job.

---

## 🔍 Problem Recap

You’re given:

- `stations[]` – sorted, strictly increasing positions on the x‑axis.  
- `k` – number of new stations you can add (any real coordinate).

**Goal:** Add exactly `k` new stations so that the maximum distance between any two consecutive stations is as small as possible.  
Return that minimal maximum distance.

---

## 🧩 Why Binary Search on the Answer Works

The answer (max distance) lies between `0` and the largest original gap.  
If a particular gap size `mid` is feasible, any larger gap will also be feasible.  
This **monotonicity** allows binary search:

```text
low  = 0
high = max(original gap)

while high - low > EPS:
    mid = (low + high) / 2
    if feasible(mid):  high = mid
    else:              low  = mid
```

Precision threshold `EPS = 1e-6` satisfies the problem’s requirement.

---

## 📐 Feasibility Check – The Greedy Core

For a candidate `mid`:

1. For every adjacent pair `(a, b)` compute `gap = b - a`.  
2. Number of new stations needed in this gap:
   \[
   needed = \lceil \frac{gap}{mid} \rceil - 1
   \]
   *Why?*  
   If we split the gap into `x` sub‑segments of size ≤ `mid`, we need `x-1` new stations.  
   `ceil(gap / mid)` gives the minimal number of segments needed.

3. Sum `needed` over all gaps.  
   - If `totalNeeded ≤ k`, `mid` is feasible.  
   - Otherwise, it’s not.

The check runs in **O(n)**, where `n` = `stations.length`.

---

## 📚 Implementation Highlights

| Language | Key Points |
|----------|------------|
| **Java** | Uses `Math.ceil` and careful type casting to `double`. `canAchieve` exits early if `needed > k`. |
| **Python** | `math.ceil` with floats; early return avoids unnecessary work. |
| **C++** | `ceil(gap / penalty)` – both operands `double`. Use `long long` for `needed` to avoid overflow (though `k ≤ 1e6`). |

All three solutions share the same structure: binary search outer loop + greedy inner loop.

---

## 📈 Time & Space

- **Time**: `O(n log(maxGap))` ≈ `O(2000 * log 1e8)` – trivial for interview constraints.  
- **Space**: `O(1)` – constant extra memory.

---

## 🌟 The Good – What Interviewers Love

1. **Elegant Binary Search on Answer** – shows you know how to reduce continuous search spaces.  
2. **Greedy Counting** – demonstrates you can compute minimum insertions efficiently.  
3. **Precision Handling** – using `1e-6` tolerance shows attention to floating‑point nuances.  
4. **Clean Code** – clear helper functions (`canAchieve`) and comments help reviewers read your logic.

---

## ⚠️ The Bad – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using integer division** in `ceil(gap / mid)` → wrong result. | Convert operands to `double`. |
| **Missing early exit** (`if (needed > k) return false;`) → wasted time for large `k`. | Add the early return inside the loop. |
| **Wrong precision threshold** → may output 0.499999 instead of 0.5. | Use `EPS = 1e-6` and loop until `high - low > EPS`. |
| **Wrong gap calculation** (`gap = stations[i] - stations[i-1]` but forgetting to cast). | Ensure `gap` is a `double`. |

---

## 🧹 The Ugly – Edge Cases & Gotchas

1. **Large `k` (up to 1e6)** – still fits easily because we only compute a simple count per gap.  
2. **Very small gaps** – `ceil(gap / mid)` may return 1, so `needed = 0`.  
3. **Floating‑point rounding** – `mid` may be slightly above true optimum; binary search stops at `EPS`, so result is within required tolerance.  
4. **Integer overflow** – not a problem in the given constraints, but be mindful in variants where `k` or `n` might be larger.  
5. **All stations are already within the same distance** – result will be 0, handled naturally by binary search.

---

## 🎯 Wrap‑Up – How to Stand Out

- **Explain monotonicity** before diving into code.  
- **Show the mathematical reasoning** for `ceil(gap / mid) - 1`.  
- **Highlight precision** and why `1e-6` suffices.  
- **Mention alternative approaches** (e.g., dynamic programming, priority queue) but justify why binary search + greedy is optimal.

These talking points illustrate depth of understanding and communication skills—exactly what recruiters look for.

---

## 🚀 Final Thoughts

LeetCode 774 may look intimidating at first glance, but once you master the *binary search on answer* pattern, the solution falls into place.  
Implementing it in your language of choice (Java, Python, C++) and explaining the algorithmic trade‑offs shows you’re ready for the *real‑world* problems that interviewers test.

Happy coding, and good luck on your next interview—your dream software‑engineering role is just a binary search away! 🌟

---

## 📌 Takeaway Checklist

- ✅ Binary search outer loop with `EPS = 1e-6`.  
- ✅ Greedy count per gap (`ceil(gap / mid) - 1`).  
- ✅ Early exit when `needed > k`.  
- ✅ Correct floating‑point operations.  
- ✅ Clear, commented helper functions.  

Check all off, and you’ll impress any hiring manager.

---

### 🎉 Happy Interviewing! 🎉

--- 

**Author:** *[Your Name]* – Coding interview coach, Java/Python/C++ enthusiast, and former interviewee who turned “the ugly” into job offers.  

--- 

*End of article.*



--- 

**Ready for your next interview?**  
Download the clean code snippets above, run through the test cases, and feel confident. Good luck!