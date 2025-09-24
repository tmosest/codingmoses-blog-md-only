---
title: LeetCode 436. Find Right Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 436. **Find Right Interval** – The Good, The Bad, and The Ugly  
> *A LeetCode Medium that’s a perfect interview‑prep problem for Java, Python and C++ developers.*

---

### 📄 Problem Recap

> **Input:** `intervals[i] = [start_i, end_i]` – all `start_i` are distinct.  
> **Goal:** For each interval *i*, find the interval *j* such that  
> `start_j >= end_i` and `start_j` is the **smallest** possible.  
> If no such *j* exists, return `-1`.  
> **Return:** an array of indices `ans[i]`.

> **Constraints**  
> * `1 ≤ intervals.length ≤ 20,000`  
> * `-10^6 ≤ start_i ≤ end_i ≤ 10^6`  
> * All `start_i` are unique.

---

### 🧠 Why This Problem Rocks in Interviews

| Aspect | Why It Matters |
|--------|----------------|
| **Sorting + Binary Search** | Classic O(n log n) pattern. |
| **Mapping original indices** | Demonstrates careful bookkeeping. |
| **Edge cases** | Handles empty results, overlapping intervals, negative values. |
| **Time/Space trade‑off** | Shows ability to optimize from O(n²) to O(n log n). |

---

## 📦 Three Implementation Languages

Below you’ll find a clean, production‑ready implementation for **Java**, **Python**, and **C++**.  
All use the *binary‑search on sorted starts* strategy (the “good” solution).

---

### ✅ 1. Java

```java
import java.util.*;

public class FindRightInterval {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[] result = new int[n];
        // Store (start, index) pairs
        int[][] starts = new int[n][2];
        for (int i = 0; i < n; i++) {
            starts[i][0] = intervals[i][0];
            starts[i][1] = i;
        }
        // Sort by start value
        Arrays.sort(starts, Comparator.comparingInt(a -> a[0]));

        for (int i = 0; i < n; i++) {
            int end = intervals[i][1];
            int idx = lowerBound(starts, end);
            result[i] = (idx == n) ? -1 : starts[idx][1];
        }
        return result;
    }

    // Classic binary search: first index where starts[idx][0] >= target
    private int lowerBound(int[][] starts, int target) {
        int lo = 0, hi = starts.length;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (starts[mid][0] < target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

---

### ✅ 2. Python

```python
from bisect import bisect_left
from typing import List

class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        n = len(intervals)
        # (start, original_index)
        starts = sorted((s, i) for i, (s, _) in enumerate(intervals))
        result = [-1] * n
        for i, (_, end) in enumerate(intervals):
            idx = bisect_left(starts, (end, -1))
            if idx < n:
                result[i] = starts[idx][1]
        return result
```

---

### ✅ 3. C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> findRightInterval(std::vector<std::vector<int>>& intervals) {
        int n = intervals.size();
        std::vector<std::pair<int, int>> starts(n);          // (start, index)
        for (int i = 0; i < n; ++i) {
            starts[i] = {intervals[i][0], i};
        }
        std::sort(starts.begin(), starts.end(),
                  [](const auto &a, const auto &b) { return a.first < b.first; });

        std::vector<int> res(n, -1);
        for (int i = 0; i < n; ++i) {
            int end = intervals[i][1];
            int idx = lower_bound(starts.begin(), starts.end(),
                                  std::make_pair(end, -1),
                                  [](const auto &p, const auto &q){ return p.first < q.first; })
                      - starts.begin();
            if (idx < n) res[i] = starts[idx].second;
        }
        return res;
    }
};
```

---

## 🔍 The Good, The Bad, and The Ugly

| Phase | Approach | Complexity | Why It’s Good / Bad |
|-------|----------|------------|---------------------|
| **1️⃣ Brute‑Force (The Ugly)** | For each interval, scan all others to find the minimal `start >= end`. | **O(n²)** time, **O(1)** space. | Works for tiny data but **fails** on 20k intervals. |
| **2️⃣ HashMap + Linear Scan (The Bad)** | Build a map `start → index`. For each interval, scan for the next start by incrementally checking. | **O(n²)** worst‑case (if no direct jump). | Simple code, but still quadratic; not acceptable for interview. |
| **3️⃣ Binary Search on Sorted Starts (The Good)** | Sort intervals by start. For each interval’s end, binary search the sorted starts. | **O(n log n)** time, **O(n)** space. | Optimal, clean, demonstrates binary‑search mastery. |
| **4️⃣ TreeMap / Ordered Map (Java‑specific)** | Use `TreeMap` to find ceiling key. | **O(n log n)** time, **O(n)** space. | Same as binary search, but more concise in Java; still optimal. |
| **5️⃣ Two‑Pointer Sweep (Advanced)** | Sort starts and ends separately, sweep line to find right interval. | **O(n log n)** time, **O(n)** space. | Good for extra practice, but more code for same performance. |

---

### 📌 Why the Binary‑Search Solution Wins

1. **Time Efficiency** – With `n ≤ 20,000`, `n log n` ≈ 20k × 15 ≈ 300k operations, trivial for any interview environment.  
2. **Deterministic** – Binary search guarantees the minimal start >= end.  
3. **Simplicity** – Only one sort and a standard `bisect`/`lower_bound`.  
4. **Space‑Friendly** – We keep an auxiliary array of size `n`.  

---

## 📈 Complexity Recap

| Operation | Time | Space |
|-----------|------|-------|
| Sort `n` starts | **O(n log n)** | **O(n)** |
| Binary search per interval | `n × O(log n)` → **O(n log n)** | **O(1)** |
| **Total** | **O(n log n)** | **O(n)** |

---

## 🛠️ Edge‑Case Checklist

| Edge Case | How We Handle It |
|-----------|------------------|
| **Only one interval** | Sorted list of size 1; binary search returns `n` → result `-1`. |
| **No interval satisfies** | Binary search returns `n` → `-1`. |
| **Negative starts/end** | No special handling; comparison works as normal. |
| **Large input** | `int` range is `-10⁶ … 10⁶`; fits into 32‑bit int. |
| **Duplicate end points** | Binary search always returns the *first* start >= end, satisfying the minimality requirement. |

---

## 🎯 How to Use This in Your Interview Prep

1. **Explain the core idea first** – sorting + binary search.  
2. **Show the code** – pick your language; the snippet above is clean and production‑ready.  
3. **Talk about time/space** – interviewer loves the concise analysis.  
4. **Mention pitfalls** – e.g., forgetting to keep original indices, mishandling `-1`.  
5. **If asked for alternatives** – discuss TreeMap (Java) or two‑pointer sweep.

---

## 📣 SEO‑Optimized Blog Headline & Meta

**Headline:**  
> Find Right Interval – The Ultimate LeetCode Medium: A Complete Guide (Java, Python, C++)  

**Meta Description:**  
> Master LeetCode #436 “Find Right Interval” with detailed explanations, top‑rated Java, Python, and C++ solutions, and interview‑ready insights. Boost your coding interview skills today!

**Keywords:**  
> LeetCode, Find Right Interval, Java, Python, C++, binary search, interview preparation, algorithm, time complexity, space complexity, optimal solution

---

## 📝 TL;DR (Table‑of‑Contents)

1. [Problem Statement](#problem-statement)  
2. [Why It Matters](#why-it-matters)  
3. [Java Implementation](#java-implementation)  
4. [Python Implementation](#python-implementation)  
5. [C++ Implementation](#c-implementation)  
6. [Good/Bad/Ugly Approaches](#goodbadugly)  
7. [Complexity Analysis](#complexity)  
8. [Edge‑Case Checklist](#edge-case)  
9. [Interview Tips](#interview-tips)  
10. [SEO Headline & Meta](#seo)  

---

## 🎓 Final Thoughts

* **Binary search on sorted starts** is the gold‑standard for this problem.  
* It’s fast, clean, and demonstrates a classic algorithmic pattern that interviewers love.  
* The solutions above are ready‑to‑copy, but always test with corner cases before a live interview.  

Good luck, and may your right intervals always align with the next best candidate! 🚀