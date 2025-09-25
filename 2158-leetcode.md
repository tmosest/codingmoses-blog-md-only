---
title: LeetCode 2158. Amount of New Area Painted Each Day - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 2158. Amount of New Area Painted Each Day – A “Good, Bad & Ugly” Deep Dive  
*LeetCode Hard – 2025‑09‑24 – Python, Java, C++*  

---

### 1️⃣ Problem Recap (SEO‑friendly intro)

> **LeetCode 2158 – Amount of New Area Painted Each Day**  
> *Hard* – **Time: O(n log n)**, **Space: O(n)**  
>  
> You’re given a 2‑D integer array `paint` where `paint[i] = [start_i, end_i]` represents the segment you must paint on day *i*.  
> Paint the same area more than once creates a “crack” – you **must count only the newly painted length each day**.  
> Return an array `worklog` where `worklog[i]` is the new area painted on day *i*.

Typical constraints  
```
1 ≤ paint.length ≤ 100 000
0 ≤ start < end ≤ 50 000
```

---

### 2️⃣ Why This Problem Is *Hard* (the “Ugly” part)

| Reason | Why it hurts |
|--------|--------------|
| **Large input** | 100 k intervals means naive O(n²) will time‑out. |
| **Overlaps can be nested** | A new segment may overlap with many existing ones; we must efficiently subtract all overlapping lengths. |
| **Dynamic updates** | After each day the painted set grows; we need to maintain it in a data structure that supports both query & merge in *log n*. |
| **Edge cases** | Adjacent intervals (`[1,4]` & `[4,7]`) *do not* overlap – careful with `<` vs `<=`. |

---

### 3️⃣ The Bad (Naive) Approach

```text
for each day i:
    area = end[i] - start[i]
    for each previous interval j:
        subtract overlap of [start[i], end[i]] with painted[j]
```

*Complexity*: **O(n²)** – impossible for 100 k.  
*Why it fails*: scanning all past intervals for each new paint leads to quadratic blow‑up.  

---

### 4️⃣ The Good – Our Optimal Strategy

1. **Maintain a set of disjoint, merged intervals** – the *painted* set.
2. **For a new interval `[s, e)`**  
   * Find the first existing interval whose start is **not after** `s` (floor).  
   * Expand leftwards if that interval overlaps `s`.  
   * While the current interval overlaps `e`, subtract the overlap from `area`, merge the two intervals, and remove the old one.  
3. **Insert the merged interval back**.  
4. **Store the computed `area`** as the answer for that day.

This is essentially the “Interval Union” problem solved with a balanced BST (Java `TreeMap`, C++ `std::map`).  
The `map` keeps intervals sorted by start; every day we touch only the intervals that truly overlap, which totals **O(n log n)** operations.

---

### 5️⃣ Implementation

#### 📌 Java (Very Easy, TreeMap)

```java
import java.util.*;

class Solution {
    // key = start, value = end
    private TreeMap<Integer, Integer> intervals = new TreeMap<>();

    public int[] amountPainted(int[][] paint) {
        int n = paint.length;
        int[] res = new int[n];

        for (int i = 0; i < n; ++i) {
            int start = paint[i][0];
            int end   = paint[i][1];
            int area  = end - start;          // initial area

            // ---- left expansion ----
            Map.Entry<Integer, Integer> left = intervals.floorEntry(start);
            while (left != null && left.getValue() > start) {
                int lStart = left.getKey(), lEnd = left.getValue();
                area -= Math.min(end, lEnd) - Math.max(start, lStart); // subtract overlap
                start = Math.min(start, lStart);
                end   = Math.max(end,   lEnd);
                intervals.remove(lStart);
                left = intervals.floorEntry(start);
            }

            // ---- right expansion ----
            Map.Entry<Integer, Integer> right = intervals.floorEntry(end);
            while (right != null && right.getValue() > start) {
                int rStart = right.getKey(), rEnd = right.getValue();
                area -= Math.min(end, rEnd) - Math.max(start, rStart);
                start = Math.min(start, rStart);
                end   = Math.max(end,   rEnd);
                intervals.remove(rStart);
                right = intervals.floorEntry(end);
            }

            res[i] = area;
            intervals.put(start, end);   // insert merged interval
        }
        return res;
    }
}
```

> **Why it’s easy**  
> *Java’s `TreeMap` gives us the needed `floorEntry`/`ceilingEntry` in O(log n).  
> The code is a one‑liner per day once the helper logic is set up.*

---

#### 📌 Python (Sorted List + Bisect)

```python
from bisect import bisect_left
from typing import List

class Solution:
    def amountPainted(self, paint: List[List[int]]) -> List[int]:
        intervals: List[List[int]] = []          # sorted by start
        res: List[int] = []

        for s, e in paint:
            area = e - s
            # Find the first interval that might overlap
            i = bisect_left(intervals, [s, 0])

            # Expand to the left if the previous interval overlaps
            if i > 0 and intervals[i-1][1] > s:
                i -= 1

            # Merge all overlapping intervals
            while i < len(intervals) and intervals[i][0] < e:
                int_s, int_e = intervals[i]
                # Subtract overlap from the area
                area -= min(e, int_e) - max(s, int_s)
                # Expand the new interval
                s = min(s, int_s)
                e = max(e, int_e)
                intervals.pop(i)          # remove merged interval

            intervals.insert(i, [s, e])   # keep list sorted
            res.append(area)

        return res
```

> **Why it works in Python**  
> *The list remains sorted; each interval is removed at most once, so the overall cost is `O(n log n)`.*

---

#### 📌 C++ (std::map)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> amountPainted(vector<vector<int>>& paint) {
        map<int,int> intervals;           // start -> end
        vector<int> res;
        for (auto &p : paint) {
            int start = p[0], end = p[1];
            int area = end - start;

            // left expansion
            auto it = intervals.upper_bound(start);
            if (it != intervals.begin()) {
                --it;
                while (it != intervals.end() && it->second > start) {
                    int lStart = it->first, lEnd = it->second;
                    area -= min(end, lEnd) - max(start, lStart);
                    start = min(start, lStart);
                    end   = max(end,   lEnd);
                    it = intervals.erase(it);
                    if (it != intervals.begin()) --it;
                }
            }

            // right expansion
            it = intervals.upper_bound(end);
            if (it != intervals.begin()) {
                --it;
                while (it != intervals.end() && it->second > start) {
                    int rStart = it->first, rEnd = it->second;
                    area -= min(end, rEnd) - max(start, rStart);
                    start = min(start, rStart);
                    end   = max(end,   rEnd);
                    it = intervals.erase(it);
                }
            }

            res.push_back(area);
            intervals[start] = end;
        }
        return res;
    }
};
```

> **C++‑style**  
> *Balanced BST guarantees `O(log n)` lookups; merging logic is almost a drop‑in copy of the Java version.*

---

### 6️⃣ Edge Cases & Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Adjacent intervals** (`[1,4]` & `[4,7]`) | Use `>` (strict) when checking overlap. `end` of one equals `start` of the next → *no overlap*. |
| **Single point intervals** (`[5,5]`) | Length is 0; our code handles it fine because `area = e - s` becomes 0. |
| **Very long chain of overlaps** | Each interval is merged *once*, so total removals = n; no quadratic blow‑up. |
| **Python list insert/pop** | Avoid `O(n)` per operation: use `bisect` for index, then `pop(i)` while scanning; we never iterate over already‑removed intervals. |

---

### 7️⃣ Complexity Analysis

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| **Java** | `O(n log n)` | `O(n)` | `TreeMap` keeps disjoint intervals. |
| **Python** | `O(n log n)` | `O(n)` | Sorted list + bisect, each interval popped at most once. |
| **C++** | `O(n log n)` | `O(n)` | `std::map` gives `upper_bound`/`lower_bound` in log time. |

Memory consumption is proportional to the number of disjoint intervals – at most *n*.

---

### 8️⃣ Take‑aways for Your Interview Toolkit

1. **Use a balanced BST (`TreeMap` / `std::map`) for interval unions.**  
   It automatically gives you sorted intervals and `floorEntry`/`upper_bound` operations in *log n*.
2. **Merge on the fly** – never keep raw, overlapping intervals.  
   Merge them into disjoint blocks as soon as you insert a new segment.
3. **Beware of “adjacent” vs “overlap”** – the problem statement explicitly says `start < end`.  
   Intervals that touch at a point are *non‑overlapping*.
4. **Test edge cases** – single‑point intervals, fully contained intervals, completely disjoint intervals, and the maximum bounds.

---

### 9️⃣ Final Thoughts

> *LeetCode 2158* is a textbook example of “take a dynamic set, query it, and merge” problems.  
> Once you master the interval‑union pattern, you can solve a variety of “count new area” or “count uncovered length” problems in **O(n log n)** time.

Feel free to copy‑paste the three implementations into your local IDE, run the official tests, and add your own test harness to play around with edge cases.  

Happy coding! 🎉

---

> **Tags:** #LeetCode #IntervalUnion #TreeMap #std::map #bisect #DynamicIntervals #O(nlogn) #Hard #Python #Java #C++  
> **Share this post** if you found the “Good, Bad & Ugly” breakdown helpful!