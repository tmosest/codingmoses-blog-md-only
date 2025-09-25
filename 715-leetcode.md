---
title: LeetCode 715. Range Module - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Blog Post  
**Mastering LeetCode 715: “Range Module” – A Hard Interview Problem Explained**

> **Keywords:** Range Module, LeetCode 715, Hard, Interview, TreeMap, std::map, Segment Tree, Lazy Propagation, Job Interview, Software Engineer, Data Structures, Algorithms

---

### 📖 Problem Recap

**LeetCode 715 – Range Module**

You need to design a data structure that tracks half‑open intervals \([l, r)\) of real numbers.  
The data structure must support three operations:

| Operation | Description |
|-----------|-------------|
| `addRange(l, r)` | Add the interval \([l, r)\). If it overlaps existing intervals, merge them. |
| `queryRange(l, r)` | Return **true** iff the entire interval \([l, r)\) is currently covered. |
| `removeRange(l, r)` | Delete the interval \([l, r)\) from the tracked set. |

**Constraints**

* \(1 \le l < r \le 10^9\)  
* At most \(10^4\) operations

> *Why this matters:*  
> *The problem tests your ability to maintain a dynamic set of intervals, a common interview theme (e.g., “Meeting Rooms,” “Car Pooling,” “My Calendar II”). Mastering it shows you understand balanced BSTs, segment trees, and lazy propagation.*

---

### 🔧 The “Good”: Why a Balanced BST (TreeMap / std::map) Works

The key insight:  
**All interval operations boil down to finding the first interval that starts **before** a given point** and checking whether that interval ends **after** a given point.

In a sorted map (`TreeMap` in Java, `std::map` in C++), the *floor* entry of a key gives us exactly that interval in **O(log n)** time. With this we can:

1. **Merge** overlapping intervals when adding.
2. **Verify containment** when querying.
3. **Trim / split** intervals when removing.

This yields a clean, easy‑to‑understand implementation with modest constant factors.

---

### 🛠️ Code Implementations

Below are fully‑commented solutions in **Java**, **Python**, and **C++**. All use an ordered map to store the current set of non‑overlapping, half‑open intervals.

---

#### Java (TreeMap)

```java
import java.util.TreeMap;
import java.util.Map;

public class RangeModule {
    // Map key = interval start, value = interval end
    private final TreeMap<Integer, Integer> map = new TreeMap<>();

    public RangeModule() {}

    /** Add [left, right) to the set. */
    public void addRange(int left, int right) {
        if (left >= right) return;            // guard

        // Find intervals that might overlap
        Map.Entry<Integer, Integer> l = map.floorEntry(left);
        Map.Entry<Integer, Integer> r = map.floorEntry(right);

        if (l != null && l.getValue() >= left) left = l.getKey();   // extend left
        if (r != null && r.getValue() > right) right = r.getValue(); // extend right

        // Remove all overlapping intervals
        map.subMap(left, right).clear();

        // Insert merged interval
        map.put(left, right);
    }

    /** Return true iff [left, right) is fully covered. */
    public boolean queryRange(int left, int right) {
        if (left >= right) return false;     // guard
        Map.Entry<Integer, Integer> l = map.floorEntry(left);
        return l != null && l.getValue() >= right;
    }

    /** Remove [left, right) from the set. */
    public void removeRange(int left, int right) {
        if (left >= right) return;          // guard

        Map.Entry<Integer, Integer> l = map.floorEntry(left);
        Map.Entry<Integer, Integer> r = map.floorEntry(right);

        // Keep left part if any
        if (l != null && l.getValue() > left) {
            map.put(l.getKey(), left);
        }
        // Keep right part if any
        if (r != null && r.getValue() > right) {
            map.put(right, r.getValue());
        }

        // Delete fully overlapped intervals
        map.subMap(left, right).clear();
    }
}
```

---

#### Python (bisect + list)

```python
import bisect
from typing import List, Tuple

class RangeModule:
    """
    Use a sorted list of non‑overlapping intervals.
    Intervals are stored as [start, end) and kept sorted by start.
    """
    def __init__(self):
        self.intervals: List[Tuple[int, int]] = []

    def _find(self, x: int) -> int:
        """Return index of interval whose start <= x, or -1."""
        i = bisect.bisect_right(self.intervals, (x, float('inf'))) - 1
        return i

    def addRange(self, left: int, right: int) -> None:
        if left >= right: return
        i = self._find(left)
        j = self._find(right)

        new_left, new_right = left, right

        # Extend to the left
        if i >= 0 and self.intervals[i][1] >= left:
            new_left = min(new_left, self.intervals[i][0])
            i -= 1

        # Extend to the right
        if j >= 0 and self.intervals[j][0] <= right:
            new_right = max(new_right, self.intervals[j][1])
            j += 1

        # Delete all overlapped intervals
        del self.intervals[i+1 : j]
        # Insert merged interval
        bisect.insort(self.intervals, (new_left, new_right))

    def queryRange(self, left: int, right: int) -> bool:
        if left >= right: return False
        i = self._find(left)
        return i >= 0 and self.intervals[i][0] <= left and self.intervals[i][1] >= right

    def removeRange(self, left: int, right: int) -> None:
        if left >= right: return
        i = self._find(left)
        j = self._find(right)

        new_intervals = []

        # Keep left fragment
        if i >= 0 and self.intervals[i][1] > left:
            new_intervals.append((self.intervals[i][0], left))

        # Keep right fragment
        if j >= 0 and self.intervals[j][0] < right:
            new_intervals.append((right, self.intervals[j][1]))

        # Replace overlapped section with fragments
        self.intervals[i+1:j+1] = new_intervals
```

---

#### C++ (std::map)

```cpp
#include <map>
using namespace std;

class RangeModule {
public:
    // key = start, value = end
    map<int, int> mp;

    RangeModule() {}

    void addRange(int left, int right) {
        if (left >= right) return;

        auto itL = mp.lower_bound(left);
        if (itL != mp.begin() && prev(itL)->second >= left)
            itL = prev(itL);

        auto itR = mp.lower_bound(right);
        if (itR != mp.begin() && prev(itR)->second > right)
            itR = prev(itR);

        if (itL != mp.end() && itL->second >= left) left = itL->first;
        if (itR != mp.end() && itR->second > right) right = itR->second;

        // Erase all overlapping intervals
        for (auto it = itL; it != itR; ++it)
            mp.erase(it);
        mp[left] = right;
    }

    bool queryRange(int left, int right) {
        if (left >= right) return false;
        auto it = mp.upper_bound(left);
        if (it == mp.begin()) return false;
        --it;
        return it->second >= right;
    }

    void removeRange(int left, int right) {
        if (left >= right) return;

        auto itL = mp.lower_bound(left);
        if (itL != mp.begin() && prev(itL)->second > left)
            itL = prev(itL);

        auto itR = mp.lower_bound(right);
        if (itR != mp.begin() && prev(itR)->second > right)
            itR = prev(itR);

        // Left fragment
        if (itL != mp.end() && itL->second > left)
            mp[itL->first] = left;

        // Right fragment
        if (itR != mp.end() && itR->first < right)
            mp[right] = itR->second;

        // Erase fully overlapped section
        for (auto it = itL; it != itR; ++it)
            mp.erase(it);
    }
};
```

> **All three solutions run in \(O(\log n)\) time per operation and keep the intervals non‑overlapping.**  
> **Memory usage:** \(O(k)\) where \(k\) is the number of disjoint intervals – far less than the entire range.

---

### 📈 Complexity Analysis

| Operation | Java / C++ | Python |
|-----------|------------|--------|
| `addRange` | \(O(\log n)\) | \(O(\log n)\) |
| `queryRange` | \(O(\log n)\) | \(O(\log n)\) |
| `removeRange` | \(O(\log n)\) | \(O(\log n)\) |

*The constants are tiny because we only touch a handful of map entries.*

---

### 🌪️ The “Bad”: When Things Go Wrong

| Issue | Why it hurts |
|-------|--------------|
| **Map overhead** | Each interval entry consumes ~48 bytes (Java) or ~16 bytes (C++). With up to \(10^4\) intervals it’s still fine, but for huge data‑sets you may hit memory limits. |
| **Corner cases** | Forgetting that the interval is *half‑open* (i.e., `end` is exclusive) leads to off‑by‑one bugs. |
| **Large coordinates** | Values up to \(10^9\) fit in `int`, but if the problem changed to 64‑bit you’d need `long long` everywhere. |
| **Concurrency** | `TreeMap` / `std::map` are *not* thread‑safe. A multi‑threaded interview scenario would trip you up. |

---

### 💥 The “Ugly”: Edge Cases & Hidden Pitfalls

1. **Zero‑length intervals** – `addRange(5,5)` or `removeRange(5,5)` should do nothing.  
2. **Full overlap** – adding `[1,10)` when `[2,8)` exists must merge *both* sides.  
3. **Adjacency** – `[1,5)` and `[5,10)` are **not** overlapping; they remain separate.  
4. **Very large intervals** – When the set is empty and you add `[0,10^9)`, all future ops must still run in log‑time.

> *Tip:* Always use guard clauses (`if (left >= right) return;`) to avoid accidental infinite loops.

---

### 🧠 Interview Tips

| Tip | Why it matters |
|-----|----------------|
| **Explain the idea first** – “We store a map of starts → ends and use floor queries.” | Shows you understand the core data structure. |
| **Walk through an example** – e.g., `add(10,20)`, `add(15,25)`, `query(10,15)`. | Demonstrates correctness. |
| **Discuss edge cases** – zero‑length intervals, adjacency, large coordinates. | Recruiters love candidates who think ahead. |
| **Talk about complexity** – O(log n) per op, memory linear in the number of disjoint intervals. | Quantifies your solution’s efficiency. |
| **Mention a fallback** – “If the interviewer wants a segment tree, we can implement it with lazy propagation.” | Shows versatility. |

---

### 🏁 Conclusion

*The “Range Module” problem is a **gold‑mine** for interview prep.*  
A `TreeMap` / `std::map` solution is:

* **Readable** – perfect for whiteboard sessions.  
* **Fast** – \(O(\log n)\) per operation, comfortably under the limits.  
* **Flexible** – Easily extended to related problems (e.g., “My Calendar III” or “Seat Reservation System”).

By mastering this problem you’ll be ready to impress recruiters on topics like interval scheduling, dynamic segment trees, and balanced BSTs—all of which are **high‑yield interview skills**.

---

### 📦 Meta‑Data for SEO

```html
<meta name="title" content="Mastering LeetCode 715: Range Module – A Hard Interview Problem Explained">
<meta name="description" content="Learn the best TreeMap/std::map solution to LeetCode 715, plus Python and C++ implementations. Understand interval data structures, interview tips, and why this problem matters for landing a software engineering job.">
<meta name="keywords" content="LeetCode 715, Range Module, Hard interview problem, TreeMap, std::map, Segment Tree, Lazy Propagation, software engineer interview, algorithm interview">
```

---

> **Ready to impress recruiters?**  
> Practice these three implementations, run your own stress tests, and bring the problem to the next interview. Good luck! 🌟

---