---
title: LeetCode 2406. Divide Intervals Into Minimum Number of Groups - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📝 LeetCode 2406 – Divide Intervals Into Minimum Number of Groups  
**Tags**: Array | Two Pointers | Greedy | Sorting | O(N log N)  

---

### TL;DR  
- **Goal** – Split the given intervals into the fewest possible groups so that no two intervals in the same group overlap.  
- **Optimal Solution** – Sort all start times and all end times, then sweep with two pointers.  
- **Complexities** – **Time** O(N log N) (sorting), **Space** O(N) (to store starts & ends).  

---

## 📌 Problem Recap  

You are given `intervals[i] = [lefti, righti]` (inclusive).  
Intervals *intersect* if they share at least one point (e.g., `[1,5]` & `[5,8]` intersect).  
Your task: **return the minimum number of groups needed** so that each group contains pairwise‑non‑overlapping intervals.

---

## 👀 Why It Matters for Interviews  

- Classic interval scheduling / meeting‑room type problem.  
- Tests ability to think in terms of **events** (starts & ends) and greedy sweeps.  
- Shows knowledge of **O(N log N)** techniques – highly valued for system‑design‑style questions.  

---

## 🏗️ The “Good” – A Simple Greedy Sweep  

1. **Separate** the start and end times into two arrays.  
2. **Sort** both arrays.  
3. Sweep through `startTimes` with a pointer `i`.  
4. Keep an `endPointer` pointing to the earliest unfinished interval.  
5. If the current start is **greater** than the earliest end → we can reuse that room → move `endPointer`.  
6. Otherwise → we need a **new group** → increment `groupCount`.  

Why does this work?  
- At any moment the number of overlapping intervals equals the number of groups required.  
- By always reusing the interval that ends the earliest, we keep the number of active groups minimal.  

---

## ❌ The “Bad” – Brute Force O(N²)  

A naive approach would compare every pair of intervals to detect conflicts, then try all groupings.  
- **Time** O(N²)  
- **Space** O(1) (or O(N²) if storing conflicts)  
- Not scalable for `N = 10⁵`.  

---

## 🥴 The “Ugly” – Over‑Engineering  

Some solutions use priority queues, segment trees, or even tree‑sets.  
While they are correct, they introduce unnecessary complexity:
- Extra log N factor (heap) on top of an already sorted sweep.  
- More code → higher chance of bugs.  
- Worse readability for interviewers.  

The beauty of the two‑pointer sweep is its elegance and linear extra memory.  

---

## 📚 Walkthrough Example  

`intervals = [[5,10],[6,8],[1,5],[2,3],[1,10]]`

1. `startTimes = [1,1,2,5,6]`  
2. `endTimes   = [3,5,8,10,10]`  

Sweep:
| i | start | endPointer | endTimes[endPointer] | action | groupCount |
|---|-------|------------|----------------------|--------|------------|
|0 | 1 | 0 | 3 | 1 ≤ 3 → **new group** | 1 |
|1 | 1 | 0 | 3 | 1 ≤ 3 → **new group** | 2 |
|2 | 2 | 0 | 3 | 2 ≤ 3 → **new group** | 3 |
|3 | 5 | 0 | 3 | 5 > 3 → reuse → endPointer=1 | 3 |
|4 | 6 | 1 | 5 | 6 > 5 → reuse → endPointer=2 | 3 |

Result: **3** groups – optimal.  

---

## 🧠 Key Takeaways  

| ✔️ | ✔️ | ✔️ |
|---|---|---|
| **Greedy** – always pick the earliest ending interval. | **Sorting** – turning the problem into a linear sweep. | **Optimal** – matches the maximum overlap at any point. |
| **Time** O(N log N) – dominated by sorting. | **Space** O(N) – to store starts & ends. | **Scalable** – works for N = 10⁵. |

---

## 🛠️ Code Implementations  

Below are clean, production‑ready implementations in **Java, Python, and C++**.

### Java (LeetCode‑compatible)

```java
import java.util.Arrays;

public class Solution {
    public int minGroups(int[][] intervals) {
        int n = intervals.length;
        int[] starts = new int[n];
        int[] ends   = new int[n];
        for (int i = 0; i < n; i++) {
            starts[i] = intervals[i][0];
            ends[i]   = intervals[i][1];
        }
        Arrays.sort(starts);
        Arrays.sort(ends);

        int endPtr = 0;
        int groups = 0;

        for (int s : starts) {
            if (s > ends[endPtr]) {        // can reuse a group
                endPtr++;
            } else {                       // need a new group
                groups++;
            }
        }
        return groups;
    }
}
```

### Python 3 (LeetCode‑compatible)

```python
from typing import List

class Solution:
    def minGroups(self, intervals: List[List[int]]) -> int:
        starts = sorted(i[0] for i in intervals)
        ends   = sorted(i[1] for i in intervals)

        end_ptr = 0
        groups = 0

        for s in starts:
            if s > ends[end_ptr]:
                end_ptr += 1        # reuse a group
            else:
                groups += 1         # new group needed

        return groups
```

### C++17 (LeetCode‑compatible)

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int minGroups(std::vector<std::vector<int>>& intervals) {
        std::vector<int> starts, ends;
        starts.reserve(intervals.size());
        ends.reserve(intervals.size());

        for (auto &iv : intervals) {
            starts.push_back(iv[0]);
            ends.push_back(iv[1]);
        }

        std::sort(starts.begin(), starts.end());
        std::sort(ends.begin(), ends.end());

        size_t endPtr = 0;
        int groups = 0;

        for (int s : starts) {
            if (s > ends[endPtr]) {          // can reuse a group
                ++endPtr;
            } else {                         // need a new group
                ++groups;
            }
        }
        return groups;
    }
};
```

All three solutions run in **O(N log N)** time and use **O(N)** extra memory – perfect for the LeetCode constraints.

---

## 🎯 SEO‑Optimized Closing  

If you’re prepping for data‑structure and algorithm interviews, mastering **Divide Intervals Into Minimum Number of Groups** will demonstrate your ability to:

- Convert interval problems into **start/end events**  
- Apply **two‑pointer sweeps** with **O(N log N)** complexity  
- Write clean, language‑agnostic solutions (Java, Python, C++)  

Add this problem to your portfolio and you’ll impress recruiters with a blend of greedy insight and coding proficiency—exactly what modern tech companies look for. Happy coding! 🚀