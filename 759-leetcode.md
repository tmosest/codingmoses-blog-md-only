---
title: LeetCode 759. Employee Free Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 759. Employee Free Time – Code, Theory & SEO‑Ready Interview Blog

## 🚀 Quick‑Start Code (Java / Python / C++)

| Language | File | Complexity |
|----------|------|------------|
| Java | **Solution.java** | **O(N log N)** time, **O(N)** space |
| Python | **solution.py** | **O(N log N)** time, **O(N)** space |
| C++ | **solution.cpp** | **O(N log N)** time, **O(N)** space |

> *N = total number of intervals across all employees (≤ 2500)*

---

### Java – Clean & Intuitive (Priority‑Queue Free)

```java
// Definition for an Interval. (LeetCode provides this.)
class Interval {
    int start, end;
    Interval() { start = 0; end = 0; }
    Interval(int s, int e) { start = s; end = e; }
}

class Solution {
    public List<Interval> employeeFreeTime(List<List<Interval>> schedule) {
        // Merge all intervals into a single list
        List<Interval> all = new ArrayList<>();
        for (List<Interval> emp : schedule) all.addAll(emp);

        // Sort by start time
        all.sort((a, b) -> Integer.compare(a.start, b.start));

        List<Interval> free = new ArrayList<>();
        Interval last = all.get(0);

        for (int i = 1; i < all.size(); i++) {
            Interval cur = all.get(i);
            if (cur.start > last.end) {          // gap found
                free.add(new Interval(last.end, cur.start));
                last = cur;
            } else {                              // overlapping → merge
                last.end = Math.max(last.end, cur.end);
            }
        }
        return free;
    }
}
```

> **Why this works** – By flattening the schedule and sorting by start time, every pair of consecutive intervals is either overlapping or separated by a free slot. The algorithm scans once, building the union of busy periods and extracting gaps.

---

### Python – Simple Merge

```python
from typing import List

class Interval:
    def __init__(self, s: int, e: int):
        self.start, self.end = s, e
    def __repr__(self):
        return f"[{self.start},{self.end}]"

class Solution:
    def employeeFreeTime(self, schedule: List[List[Interval]]) -> List[Interval]:
        # 1️⃣ Flatten
        all_int = [it for emp in schedule for it in emp]
        # 2️⃣ Sort by start
        all_int.sort(key=lambda it: it.start)

        free, cur = [], all_int[0]

        for nxt in all_int[1:]:
            if nxt.start > cur.end:          # gap
                free.append(Interval(cur.end, nxt.start))
                cur = nxt
            else:                            # merge
                cur.end = max(cur.end, nxt.end)

        return free
```

---

### C++ – STL + Merge

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Interval {
    int start, end;
    Interval() : start(0), end(0) {}
    Interval(int s, int e) : start(s), end(e) {}
};

class Solution {
public:
    vector<Interval> employeeFreeTime(vector<vector<Interval>>& schedule) {
        vector<Interval> all;
        for (auto& emp : schedule)
            for (auto& it : emp)
                all.push_back(it);

        sort(all.begin(), all.end(),
             [](const Interval& a, const Interval& b){ return a.start < b.start; });

        vector<Interval> free;
        Interval cur = all[0];
        for (size_t i = 1; i < all.size(); ++i) {
            const Interval& nxt = all[i];
            if (nxt.start > cur.end) {            // free slot
                free.emplace_back(cur.end, nxt.start);
                cur = nxt;
            } else {                               // merge
                cur.end = max(cur.end, nxt.end);
            }
        }
        return free;
    }
};
```

> All three snippets share the same *time* and *space* footprint: **O(N log N)** time due to the sort, **O(N)** auxiliary memory.

---

## 📖 Blog Article – “The Good, The Bad, and The Ugly of LeetCode 759”

> **SEO Keywords**: *Employee Free Time*, *LeetCode 759*, *job interview algorithm*, *priority queue*, *sweep line*, *Java solution*, *Python solution*, *C++ solution*, *software engineering interview*, *coding interview tips*.

---

### 1. Introduction

When you open LeetCode problem **759 – Employee Free Time**, you’re looking at a classic interval‑merging challenge. The prompt asks you to find *finite* intervals where **every** employee is simultaneously free. This problem is a favorite among interviewers because it touches on:

- **Sorting & merging** – a staple for time‑complexity analysis.
- **Sweep‑line / priority‑queue** – a common interview pattern.
- **Edge‑case handling** – empty intervals, infinite borders, and zero‑length gaps.

In this post, we’ll dissect the *good*, *bad*, and *ugly* aspects of typical solutions, reveal a production‑ready implementation, and sprinkle interview‑ready tips that’ll help you land that job.

---

### 2. The Problem Re‑phrased

> **Given** a `schedule`, a list of lists where each inner list contains non‑overlapping, sorted `Interval` objects for a single employee.  
> **Return** the sorted list of finite, positive‑length intervals that represent common free time for **all** employees.

*Important constraints*

| # | Constraint |
|---|------------|
| 1 | `1 <= schedule.length <= 50` |
| 2 | `1 <= schedule[i].length <= 50` |
| 3 | `0 <= start < end <= 10^8` |

---

### 3. The Good – Clean Merge Strategy

The most straightforward approach is **flatten‑and‑sort**:

1. **Flatten** all intervals into one list.  
2. **Sort** by `start` time.  
3. **Scan** once, merging overlapping intervals and collecting gaps.

Why is this *good*?

| Aspect | Reason |
|--------|--------|
| **Simplicity** | One sort + linear scan; easy to implement and understand. |
| **Correctness** | Sorting guarantees that all overlapping intervals become contiguous, making gaps obvious. |
| **Time** | `O(N log N)` due to sorting (N = total intervals). |
| **Space** | `O(N)` for the flattened list and result. |

The Java, Python, and C++ snippets above illustrate this pattern.

---

### 4. The Bad – Misusing a Priority Queue

Many beginners think that a **min‑heap** of `start` times is necessary. While a heap can solve *k‑sorted‑list* problems, it introduces unnecessary complexity for 759:

- **Overkill** – A heap offers `O(N log K)` operations where `K ≤ 50`, but sorting gives `O(N log N)` and is simpler.
- **Bug‑prone** – Managing pointers, popping, and re‑inserting intervals can lead to subtle bugs (e.g., missing the last employee’s intervals).
- **Memory waste** – The heap stores duplicates of interval objects or references.

In interview settings, the clean merge approach is often favored. If you do use a heap, be sure to explain its advantages and trade‑offs transparently.

---

### 5. The Ugly – Skipping Edge Cases

- **Infinite borders** – The problem explicitly states that intervals like `[-∞, 1]` and `[10, ∞]` are *not* included. Forgetting to discard them yields wrong answers.
- **Zero‑length gaps** – Intervals `[5, 5]` should not appear. Check `if (gap.start < gap.end)` before adding to the result.
- **Empty schedules** – An empty `schedule` should return an empty list, not an error.
- **Large values** – Avoid integer overflow when computing `max(start, end)`; use `long` if needed.

Failing to handle these cases can cost you in an interview or in production code.

---

### 6. Intersecting Intervals: A Step‑by‑Step Walkthrough

Let’s walk through the Java solution on the example:

```
schedule = [
  [[1,2], [5,6]],
  [[1,3]],
  [[4,10]]
]
```

1. **Flatten** → `[(1,2), (5,6), (1,3), (4,10)]`
2. **Sort** → `[(1,2), (1,3), (4,10), (5,6)]`
3. **Scan**  
   - `last = (1,2)` → next `(1,3)` overlaps → merge → `last = (1,3)`  
   - next `(4,10)` → `gap = (3,4)` → add to result → `last = (4,10)`  
   - next `(5,6)` → overlaps → merge → `last = (4,10)`  
4. **Result** → `[(3,4)]`

The algorithm runs in linear time after sorting, which is optimal for this problem.

---

### 7. Performance & Complexity Analysis

| Operation | Java | Python | C++ |
|-----------|------|--------|-----|
| Flatten & sort | `O(N log N)` | `O(N log N)` | `O(N log N)` |
| Linear scan | `O(N)` | `O(N)` | `O(N)` |
| Total time | **≈ 2500 log 2500** ≈ 35k comparisons | Same | Same |
| Memory (aux) | `O(N)` | `O(N)` | `O(N)` |
| Result size | `O(G)` where G = number of free gaps | Same | Same |

*Why not better?*  
The union of busy intervals is a classic *interval covering* problem. You cannot beat `O(N log N)` unless the input is already sorted (not guaranteed here). A linear scan after sorting is the gold standard.

---

### 8. Interview‑Ready Tips

1. **Explain your approach first** – Show the interviewer you’re thinking through the problem: “I’ll flatten, sort, then merge.”
2. **Mention edge cases** – Infinite borders, zero‑length gaps, and empty inputs. Demonstrating awareness of constraints often impresses interviewers.
3. **Discuss alternative patterns** – “Could a sweep‑line or heap work? Yes, but I prefer the flat‑merge because it’s simpler and just as fast.”
4. **Time‑complexity on the whiteboard** – Write `O(N log N)` and note that N is the total number of intervals, not just the number of employees.
5. **Ask clarifying questions** – If the problem statement is ambiguous (e.g., “Do intervals touching the border count?”), it’s safe to ask. Interviewers appreciate candidates who verify assumptions.

---

### 8. Real‑World Application

In a real workplace, employee schedules are dynamic. The *flatten‑and‑sort* method can be adapted to **incremental updates**:

- **Add / remove** an interval → re‑flatten the affected employee’s list and re‑run the algorithm.  
- **Parallelize** – Each employee’s list is already sorted; you can use *merge‑sort* on the flattened list to leverage multi‑threading if N is huge (rare for 759, but a useful pattern for large‑scale scheduling systems).

---

### 9. Takeaway – A Code & Career Checklist

| ✅ | ✅ | ✅ |
|---|---|---|
| ✔ Implement a **clean merge** solution (no heap needed). |
| ✔ **Edge‑case** checklist: discard infinite gaps, ignore zero‑length intervals. |
| ✔ Explain *why* your algorithm works, including time & space analysis. |
| ✔ Ask for clarification on ambiguous constraints. |
| ✔ Keep the code **readable** – use meaningful variable names (`lastBusy`, `gap`). |
| ✔ Practice coding on whiteboard; remember to handle an empty schedule. |

If you can walk through the above with confidence, you’ll demonstrate mastery of both the algorithmic core and the subtle pitfalls – exactly what interviewers look for.

---

### 10. Final Thoughts

LeetCode 759 is more than a test of interval merging; it’s a test of how you *present* your solution. The *good* pattern—flatten, sort, and scan—remains the most robust for this problem. Avoid the *bad* heap pitfalls unless you have a compelling reason, and never ignore the *ugly* edge cases.

Use these snippets as a reference, internalize the analysis, and let your interview show that you’re not just a coder, but a thoughtful software engineer who can translate theory into clean, production‑ready code.

> **Good luck, code hard, and make that interview call feel like a walk in the park!**

--- 

*If you found this helpful, drop a comment or share it on LinkedIn with the tags above. Happy coding!* 

--- 

**Happy Interviewing!** 🚀