---
title: LeetCode 715. Range Module - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 715 – **Range Module**  
**Hard | 10⁴ operations | 10⁹ range**  
**LeetCode**: <https://leetcode.com/problems/range-module/>

> A `RangeModule` tracks a set of half‑open intervals \([l,r)\).  
>  
> * `addRange(l,r)` – add an interval, merging with any existing ones.  
> * `queryRange(l,r)` – return `true` if the entire interval is currently covered.  
> * `removeRange(l,r)` – delete the interval from the tracked set.  

Below you’ll find three clean, production‑ready solutions – **Java (TreeMap)**, **Python (bisect + sorted list)**, **C++ (std::map)** – each with O(log n) time per operation.  
After the code, read the SEO‑friendly blog article that explains *the good, the bad, and the ugly* of this problem, how it’s often used in technical interviews, and why mastering it can boost your job prospects.

---

## 1. Java – TreeMap (Fast, Easy to Understand)

```java
import java.util.*;

public class RangeModule {
    /**  key = start, value = end  (exclusive) */
    private final TreeMap<Integer, Integer> ranges = new TreeMap<>();

    public RangeModule() {}

    /** Add [l, r) */
    public void addRange(int l, int r) {
        // Find leftmost overlapping interval
        Map.Entry<Integer, Integer> left = ranges.floorEntry(l);
        if (left != null && left.getValue() >= l) l = left.getKey();

        // Find rightmost overlapping interval
        Map.Entry<Integer, Integer> right = ranges.floorEntry(r);
        if (right != null && right.getValue() > r) r = right.getValue();

        // Remove all fully overlapped ranges
        ranges.subMap(l, r).clear();

        // Insert the merged interval
        ranges.put(l, r);
    }

    /** Return true if [l, r) is fully covered */
    public boolean queryRange(int l, int r) {
        Map.Entry<Integer, Integer> left = ranges.floorEntry(l);
        return left != null && left.getValue() >= r;
    }

    /** Remove [l, r) */
    public void removeRange(int l, int r) {
        Map.Entry<Integer, Integer> left = ranges.floorEntry(l);
        if (left != null && left.getValue() > l) {
            ranges.put(left.getKey(), l);   // keep left side
        }

        Map.Entry<Integer, Integer> right = ranges.floorEntry(r);
        if (right != null && right.getValue() > r) {
            ranges.put(r, right.getValue()); // keep right side
        }

        // Erase all ranges that fall inside [l, r)
        ranges.subMap(l, r).clear();
    }
}
```

**Why TreeMap?**  
- Keeps intervals sorted by start point.  
- `floorEntry` gives the largest key ≤ target, making overlap checks O(log n).  
- `subMap` allows bulk removal of all fully overlapped intervals.

---

## 2. Python – SortedList + bisect

```python
import bisect
from typing import List

class RangeModule:
    def __init__(self):
        self.starts: List[int] = []  # start points
        self.ends: List[int] = []    # end points (exclusive)

    def _find_left(self, x: int) -> int:
        """Largest index i such that starts[i] <= x"""
        i = bisect.bisect_right(self.starts, x) - 1
        return i

    def addRange(self, left: int, right: int) -> None:
        i = self._find_left(left)
        if i >= 0 and self.ends[i] >= left:
            left = self.starts[i]
        j = self._find_left(right)
        if j >= 0 and self.ends[j] > right:
            right = self.ends[j]

        # Delete overlapping intervals
        del self.starts[max(0, i+1):j+1]
        del self.ends[max(0, i+1):j+1]

        # Insert merged interval
        pos = bisect.bisect_left(self.starts, left)
        self.starts.insert(pos, left)
        self.ends.insert(pos, right)

    def queryRange(self, left: int, right: int) -> bool:
        i = self._find_left(left)
        return i >= 0 and self.ends[i] >= right

    def removeRange(self, left: int, right: int) -> None:
        i = self._find_left(left)
        if i >= 0 and self.ends[i] > left:
            self.ends[i] = left          # truncate left part

        j = self._find_left(right)
        if j >= 0 and self.ends[j] > right:
            # Insert a new interval for the right part
            pos = bisect.bisect_left(self.starts, right)
            self.starts.insert(pos, right)
            self.ends.insert(pos, self.ends[j])
            self.ends[j] = right

        # Delete fully overlapped intervals
        del self.starts[max(0, i+1):j+1]
        del self.ends[max(0, i+1):j+1]
```

**Why bisect + lists?**  
- Python’s `bisect` gives O(log n) search.  
- Two parallel lists keep start/end pairs, making memory footprint small.  
- All modifications stay O(log n) plus O(k) for removing `k` overlapping intervals (k ≤ n).

---

## 3. C++ – `std::map` (ordered associative container)

```cpp
#include <map>

class RangeModule {
    std::map<int, int> mp;          // key = start, value = end (exclusive)

public:
    RangeModule() {}

    void addRange(int left, int right) {
        auto it = mp.lower_bound(left);
        if (it != mp.begin()) {
            auto prev = std::prev(it);
            if (prev->second >= left) left = prev->first;
        }
        while (it != mp.end() && it->first <= right) {
            right = std::max(right, it->second);
            it = mp.erase(it);
        }
        mp.emplace(left, right);
    }

    bool queryRange(int left, int right) {
        auto it = mp.upper_bound(left);
        if (it == mp.begin()) return false;
        --it;
        return it->second >= right;
    }

    void removeRange(int left, int right) {
        auto it = mp.lower_bound(left);
        if (it != mp.begin()) {
            auto prev = std::prev(it);
            if (prev->second > left) {
                int old_end = prev->second;
                prev->second = left;
                if (old_end > right) {
                    mp.emplace(right, old_end);
                }
            }
        }
        while (it != mp.end() && it->first < right) {
            if (it->second > right) {
                mp.emplace(right, it->second);
            }
            it = mp.erase(it);
        }
    }
};
```

**Why `std::map`?**  
- Logarithmic insert, delete, and search.  
- Clear syntax for split/merge logic.  
- Works well in competitive programming environments.

---

## 4. The Good, the Bad, & the Ugly

| Aspect | What’s good | Potential pitfalls | What to avoid |
|--------|-------------|--------------------|---------------|
| **Data structure** | Ordered maps keep intervals sorted → O(log n) operations | If you use a *list* or *vector*, operations become O(n) | Avoid naïve linear scans after each update |
| **Merge logic** | Simple “expand left/right” approach merges all overlapping ranges in one pass | Forgetting to handle *adjacent* intervals (e.g., `[1,3)` and `[3,5)`) → may leave gaps | Always treat intervals as *half‑open* |
| **Deletion** | Split left/right parts if they exist → no lost data | Over‑deleting or forgetting to keep the right fragment | Keep track of the original interval’s end before erasing |
| **Memory** | Map stores only *disjoint* intervals | Repeated add/remove can fragment the map → many small ranges | Merge aggressively in `addRange` to keep size minimal |
| **Complexity** | Each operation is *logarithmic* | In worst case, removing many tiny intervals → O(k log n) | Use `subMap` / `erase` in bulk where possible |
| **Language quirks** | Java TreeMap, C++ map, Python bisect | Java’s `subMap` returns a view – must call `clear()`; Python’s list deletion slice is O(k) | Pay attention to boundary conditions (right exclusive) |

### The “Ugly” Side

- **Edge Cases**:  
  - Adding a range that touches the end of an existing one.  
  - Removing a range that partially overlaps multiple intervals.  
  - Querying an empty set or range outside all intervals.

- **Off‑by‑One**:  
  Since intervals are *half‑open*, a range `[10, 20)` includes 10 but excludes 20.  
  Mistakenly treating it as closed can lead to subtle bugs.

- **Thread Safety**:  
  The given implementations are *not* thread‑safe.  
  In a multi‑threaded environment you’ll need external locks or a concurrent map.

---

## 5. Why Mastering Range Module Helps Your Career

1. **Interview Frequency**  
   - *Range Module* is a staple on technical hiring platforms (LeetCode, HackerRank, InterviewBit).  
   - It tests your ability to maintain a *dynamic set* of intervals – a classic data‑structure problem.

2. **Conceptual Breadth**  
   - You’ll practice:  
     *Ordered maps / balanced BSTs* → core data‑structure knowledge.  
     *Interval arithmetic* → math + edge‑case handling.  
     *Set operations (union, intersection, difference)* → fundamental to many systems.

3. **Showcasing Problem‑Solving**  
   - Write a clear, bug‑free implementation in the language you’re comfortable with.  
   - Explain the time‑space trade‑offs during an interview; it shows depth of understanding.

4. **Production Relevance**  
   - Many real‑world services (e.g., feature‑flag engines, scheduling systems, network firewall rules) maintain ranges of timestamps or IP blocks.  
   - Knowing this pattern gives you confidence to contribute immediately to such systems.

---

## 5. SEO‑Friendly Blog Title & Outline

**Title**  
> “Range Module – The Interview Power‑Up: 715 LeetCode Explained (Good, Bad & Ugly)”

**Meta Description**  
> Dive into the “Range Module” problem (LeetCode #715). Learn three O(log n) solutions in Java, Python, and C++, plus interview‑ready insights on edge‑case handling and why mastering this problem can land you a software‑engineering role.

**Suggested Headings**

1. **What is the Range Module?** – Problem statement & half‑open intervals.  
2. **Why This Problem Rocks for Interviews** – Dynamic set of intervals, time‑space trade‑offs.  
3. **Three Production‑Ready Implementations** – Java, Python, C++ (code blocks).  
4. **The Good** – Ordered maps, logarithmic ops, clear split‑merge logic.  
5. **The Bad** – Edge‑cases, off‑by‑one errors, fragmentation.  
6. **The Ugly** – Adjacent intervals, thread safety, large‑scale fragmentation.  
7. **Common Mistakes to Avoid** – Pseudocode checklist, boundary tests.  
8. **Beyond the Problem** – Applying interval logic to scheduling, caching, firewall rules.  
9. **Interview Success Tips** – How to explain your solution, trade‑off discussions, handling follow‑up questions.  
10. **Conclusion & Next Steps** – Further problems (Merge Intervals, 315 – Count of Smaller Numbers, 720 – Longest Word in Dictionary) and resources.

---

### Takeaway

Mastering **Range Module** means you can:

- Write efficient, bug‑free code for dynamic interval problems.  
- Articulate trade‑offs between different languages and data structures.  
- Solve a classic interview problem that tests both data‑structure knowledge and careful reasoning about edge cases.

Give the solutions a try, run through a handful of random tests, and feel the confidence that comes from being *pro‑fessional* at managing ranges. Happy coding—and good luck on your next interview!