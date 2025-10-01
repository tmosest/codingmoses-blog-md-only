---
title: LeetCode 715. Range Module - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Range Module – Leetcode 715  
> **Hard** | **Java** | **Python** | **C++** | **Job‑Interview Readiness** | **SEO‑Optimized**  

## 1.  Problem Recap (What you’ll see on Leetcode)

```
RangeModule()                  // initialise
void addRange(int left, int right)      // add [left, right)
boolean queryRange(int left, int right) // is every x ∈ [left, right) tracked?
void removeRange(int left, int right)   // stop tracking [left, right)
```

*`left < right`, values up to 10⁹, ≤ 10⁴ total calls.*  
We need a data structure that can insert, query, and delete *half‑open* integer intervals in **O(log N)** time, where *N* is the number of disjoint ranges stored.

---

## 2.  “The Good, The Bad, The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Data‑structure choice** | Balanced BST (Java `TreeMap`, C++ `std::map`, Python `SortedList` / `bisect`) | None – always *O(log N)* | Heavyweight segment trees with lazy propagation (overkill for 10⁴ ops) |
| **Implementation complexity** | ~30 lines of clean code | Handling edge cases (overlap, adjacency) | Lots of pointer gymnastics, debug‑painful |
| **Space overhead** | O(N) – one node per disjoint interval | None | Segment tree uses O(4 N) nodes |
| **Readability for interview** | Very high | Requires clear explanation of `floorEntry` semantics | Very low; interviewers rarely expect lazy‑prop code |
| **Potential bugs** | Forgetting half‑open convention, off‑by‑one errors | None if careful | Over‑merging / missing split parts |

> **TL;DR** – The *TreeMap / map* solution is the sweet spot: fast, small, and readable. The segment‑tree alternative is elegant but too verbose for most interview scenarios.

---

## 3.  The Idea – Keep a Sorted Set of *Disjoint* Intervals

- All intervals in the data structure are **non‑overlapping** and **sorted by start**.  
- When we add a new range we:

  1. Merge it with any existing intervals that overlap or touch it.  
  2. Delete those overlapped intervals.  
  3. Insert the merged interval.

- When we query we just need to check the immediate predecessor interval: does it start before the query left and end after or at the query right?

- When we remove a range we:

  1. Possibly split the leftmost overlapping interval at the start of the removal.  
  2. Possibly split the rightmost overlapping interval at the end of the removal.  
  3. Delete every interval completely inside the removal.

Because the intervals are sorted, all operations touch only *O(log N)* nodes.

---

## 4.  Java Implementation (TreeMap)

```java
import java.util.*;

class RangeModule {
    /** map: key = start, value = end (exclusive) of a disjoint interval */
    private final TreeMap<Integer, Integer> map = new TreeMap<>();

    public RangeModule() {}

    /** Add [left, right) */
    public void addRange(int left, int right) {
        // 1. Find possible left & right overlapping intervals
        Map.Entry<Integer, Integer> l = map.floorEntry(left);
        Map.Entry<Integer, Integer> r = map.floorEntry(right);

        // 2. Merge with left overlap
        if (l != null && l.getValue() >= left) {
            left = l.getKey();
        }

        // 3. Merge with right overlap
        if (r != null && r.getValue() > right) {
            right = r.getValue();
        }

        // 4. Remove all fully covered intervals
        map.subMap(left, right).clear();

        // 5. Insert merged interval
        map.put(left, right);
    }

    /** Return true if [left, right) is fully covered */
    public boolean queryRange(int left, int right) {
        Map.Entry<Integer, Integer> l = map.floorEntry(left);
        return l != null && l.getValue() >= right;
    }

    /** Remove [left, right) */
    public void removeRange(int left, int right) {
        Map.Entry<Integer, Integer> l = map.floorEntry(left);
        Map.Entry<Integer, Integer> r = map.floorEntry(right);

        // If left part of an overlapping interval remains
        if (l != null && l.getValue() > left) {
            map.put(l.getKey(), left);
        }

        // If right part of an overlapping interval remains
        if (r != null && r.getValue() > right) {
            map.put(right, r.getValue());
        }

        // Delete everything inside [left, right)
        map.subMap(left, right).clear();
    }
}
```

**Why this works:**  
- `floorEntry` gives us the interval whose start is ≤ a given point.  
- `subMap(fromKey, toKey)` (exclusive of `toKey`) lets us delete a contiguous slice in O(log N).  
- All operations are `O(log N)`.

---

## 5.  Python Implementation (SortedList + bisect)

```python
import bisect

class RangeModule:
    def __init__(self):
        # list of starts and corresponding ends (both sorted)
        self.starts = []  # start of intervals
        self.ends   = []  # end of intervals

    def addRange(self, left: int, right: int) -> None:
        i = bisect.bisect_left(self.ends, left)   # first interval that ends after left
        j = bisect.bisect_left(self.ends, right)  # first interval that ends after right

        # Expand boundaries if needed
        if i > 0 and self.ends[i-1] >= left:
            left = self.starts[i-1]
        if j < len(self.ends) and self.ends[j] > right:
            right = self.ends[j]

        # Remove overlapped intervals
        self.starts[i:j] = [left]
        self.ends[i:j]   = [right]

    def queryRange(self, left: int, right: int) -> bool:
        i = bisect.bisect_right(self.starts, left) - 1
        return i >= 0 and self.ends[i] >= right

    def removeRange(self, left: int, right: int) -> None:
        i = bisect.bisect_left(self.ends, left)
        j = bisect.bisect_left(self.ends, right)

        # Keep left fragment
        if i > 0 and self.ends[i-1] > left:
            self.ends[i-1] = left

        # Keep right fragment
        if j < len(self.ends) and self.ends[j] > right:
            self.starts[j] = right

        # Delete fully covered intervals
        del self.starts[i:j]
        del self.ends[i:j]
```

*The Python code mirrors the Java logic but uses two parallel lists with `bisect`. It stays in `O(log N)` time.*

---

## 6.  C++ Implementation (std::map)

```cpp
#include <map>

class RangeModule {
    std::map<int, int> mp;          // key = start, value = end (exclusive)

public:
    RangeModule() {}

    void addRange(int left, int right) {
        auto l = mp.lower_bound(left);
        if (l != mp.begin()) {
            auto prev = std::prev(l);
            if (prev->second >= left) left = prev->first;
        }

        auto r = mp.lower_bound(right);
        if (r != mp.begin()) {
            auto prev = std::prev(r);
            if (prev->second > right) right = prev->second;
        }

        // erase all overlapping intervals
        auto it = mp.lower_bound(left);
        while (it != mp.end() && it->first < right) it = mp.erase(it);
        mp[left] = right;
    }

    bool queryRange(int left, int right) {
        auto it = mp.upper_bound(left);
        if (it == mp.begin()) return false;
        --it;
        return it->second >= right;
    }

    void removeRange(int left, int right) {
        auto l = mp.lower_bound(left);
        if (l != mp.begin()) {
            auto prev = std::prev(l);
            if (prev->second > left) {
                mp[prev->first] = left;          // left fragment
            }
        }

        auto r = mp.lower_bound(right);
        if (r != mp.begin()) {
            auto prev = std::prev(r);
            if (prev->second > right) {
                mp[right] = prev->second;        // right fragment
            }
        }

        // erase fully covered
        it = mp.lower_bound(left);
        while (it != mp.end() && it->first < right) it = mp.erase(it);
    }
};
```

> **Note:** The C++ version is almost identical to the Java solution but uses iterators instead of `floorEntry`.

---

## 7.  Complexity Analysis

| Operation | Time | Extra Space |
|-----------|------|-------------|
| `addRange` | `O(log N)` | `O(1)` new node |
| `queryRange` | `O(log N)` | none |
| `removeRange` | `O(log N)` | possible two new nodes (splits) |

`N` is the number of stored disjoint ranges (≤ 10⁴).  
Because each call touches at most one predecessor and one successor, the algorithm never traverses more than a handful of map entries.

---

## 8.  Interview Tips

1. **State the invariant** – “All intervals are disjoint and sorted by start.”  
2. **Explain `floorEntry` / `upper_bound`** – why the predecessor interval matters for a query.  
3. **Clarify half‑open nature** – `[l, r)` never includes `r`.  
4. **Show edge‑case handling** – adjacency (`[1,3)` + `[3,5)` → `[1,5)`).  
5. **Mention complexity** – “All three operations run in O(log N) time, which is fast enough for 10⁴ calls.”  

> These talking points are exactly what recruiters expect: you know the data‑structure, you can reason about invariants, and you handle off‑by‑one pitfalls.

---

## 9.  SEO‑Friendly Meta‑Summary (for your blog)

> **Title:** Range Module – Leetcode 715 | O(log N) BST Solution (Java, Python, C++)  
> **Description:** Master Leetcode 715 “Range Module” with a concise `TreeMap`/`std::map` implementation. Get ready for coding interviews and optimize your job‑search content with clear explanations, code snippets, and complexity analysis.  
> **Keywords:** Leetcode 715, Range Module, interval tree, TreeMap, std::map, SortedList, bisect, coding interview, job interview, algorithm analysis, O(log N) operations.

---

## 10.  Conclusion

The *TreeMap / `std::map`* approach is the most interview‑friendly, balancing speed, memory, and clarity. It runs in `O(log N)` time for all three operations, keeps the intervals disjoint, and is only a few dozen lines of code.

If you’re prepping for a software‑engineering interview, **present the BST solution**, walk through the three operations, and emphasise the half‑open interval nuance. You’ll impress the interviewer with both your coding skill and your algorithmic intuition.

Happy coding—and good luck on your next interview!