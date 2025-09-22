---
title: LeetCode 3323. Minimize Connected Groups by Inserting Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Mastering LeetCode 3323: **Minimize Connected Groups by Inserting Interval**  
> *Java | Python | C++ – One Sliding‑Window Solution that Passes 1 e5*  

---

## 1. Problem Overview  

You’re given an array of **intervals** (each `[start, end]`) and a maximum allowed length `k` for a *new* interval that you must insert exactly once.  
After inserting the interval, you want to **minimise** the number of *connected groups* of intervals.  

A *connected group* is a maximal set of intervals that together cover a continuous range with no gaps.  
Example:

| Intervals | Connected? |
|-----------|------------|
| `[[1,3],[3,5],[5,6]]` | ✅ (covers 1–6) |
| `[[1,2],[3,4]]` | ❌ (gap 2–3) |

**Return** the smallest possible number of connected groups after adding the new interval.

> **Constraints**  
> * 1 ≤ `intervals.length` ≤ 10⁵  
> * 1 ≤ `start` ≤ `end` ≤ 10⁹  
> * 1 ≤ `k` ≤ 10⁹  

---

## 2. Why This Problem Is Hard  

* **Large Input Size** – O(n log n) is acceptable, but anything quadratic will TLE.  
* **Insertion Must Cover Gaps** – You can’t simply “merge” intervals; you have to think about how the new interval can span **multiple** gaps at once.  
* **Sliding Window on Gaps** – The key trick is to treat gaps between merged intervals as a sequence and use a sliding window to find the longest contiguous subsequence of gaps that can be covered by a single interval of length ≤ k.  

---

## 3. Core Insight  

1. **Merge the given intervals** – After merging, each consecutive pair of merged intervals is separated by a *gap* of length `gapStartGapEnd`.  
2. **Gaps are sorted** – Because we sorted the original intervals by start, the gaps are automatically in ascending order of their start positions.  
3. **Covering a contiguous block of gaps** – If we choose a window `[l … r]` of gaps, the new interval must span from the *start* of the first gap to the *end* of the last gap.  
   *Required length* = `gaps[r].end - gaps[l].start`.  
4. **Sliding Window** – Move a right pointer, adjust the left pointer when the required length exceeds `k`. Keep track of the maximum number of gaps (`r-l+1`) that fit within the limit.  
5. **Answer** – Each gap merged reduces the group count by one.  
   \[
   \text{answer} = \text{groups} - \max_{\text{window}}\#\text{gaps} = (\text{gaps} + 1) - \maxCovered = \text{groups} - \maxCovered
   \]

---

## 4. Algorithm Steps  

1. **Sort intervals** by start ascending; if equal, by end descending (so we can merge easily).  
2. **Merge** overlapping or touching intervals → `merged`.  
3. **Collect gaps** between consecutive `merged` intervals → `gaps`.  
4. **Sliding Window on gaps**  
   * `l = 0`, `maxCovered = 0`  
   * For each `r` in `[0 … gaps.size-1]`  
     * While `requiredLength > k`, increment `l`.  
     * Update `maxCovered = max(maxCovered, r-l+1)`.  
5. **Return** `merged.size() - maxCovered`.  

---

## 5. Complexity Analysis  

| Step | Time | Space |
|------|------|-------|
| Sort | **O(n log n)** | O(1) auxiliary (sorting in place) |
| Merge | **O(n)** | O(1) |
| Gaps | **O(m)** where *m* = merged.size() | O(m) |
| Sliding Window | **O(m)** | O(1) |
| **Total** | **O(n log n)** | **O(n)** (worst‑case for gaps) |

This comfortably satisfies the constraints.

---

## 6. Implementation – Java  

```java
import java.util.*;

public class Solution {
    public int minConnectedGroups(int[][] intervals, int k) {
        if (intervals == null || intervals.length == 0) return 0;

        // 1. Sort by start; tie‑break by end descending
        Arrays.sort(intervals, (a, b) -> {
            if (a[0] == b[0]) return Integer.compare(b[1], a[1]);
            return Integer.compare(a[0], b[0]);
        });

        // 2. Merge intervals
        List<int[]> merged = new ArrayList<>();
        int curStart = intervals[0][0];
        int curEnd   = intervals[0][1];
        for (int i = 1; i < intervals.length; i++) {
            int[] cur = intervals[i];
            if (cur[0] <= curEnd) {               // overlap or touch
                curEnd = Math.max(curEnd, cur[1]);
            } else {                               // disjoint
                merged.add(new int[]{curStart, curEnd});
                curStart = cur[0];
                curEnd   = cur[1];
            }
        }
        merged.add(new int[]{curStart, curEnd});

        // 3. Gather gaps between merged intervals
        int m = merged.size();
        if (m == 1) return 1;                // only one group, nothing to bridge
        List<int[]> gaps = new ArrayList<>();
        for (int i = 0; i < m - 1; i++) {
            int end1 = merged.get(i)[1];
            int start2 = merged.get(i + 1)[0];
            gaps.add(new int[]{end1, start2});   // gap: (end1, start2)
        }

        // 4. Sliding window over gaps
        int l = 0, maxCovered = 0;
        for (int r = 0; r < gaps.size(); r++) {
            // required length to cover gaps[l..r]
            while (l <= r && gaps.get(r)[1] - gaps.get(l)[0] > k) {
                l++;
            }
            maxCovered = Math.max(maxCovered, r - l + 1);
        }

        // 5. Result: groups - maxCovered
        return merged.size() - maxCovered;
    }
}
```

---

## 7. Implementation – Python  

```python
from typing import List

class Solution:
    def minConnectedGroups(self, intervals: List[List[int]], k: int) -> int:
        intervals.sort(key=lambda x: (x[0], -x[1]))

        # Merge
        merged = []
        cur_start, cur_end = intervals[0]
        for s, e in intervals[1:]:
            if s <= cur_end:                 # overlap / touch
                cur_end = max(cur_end, e)
            else:
                merged.append((cur_start, cur_end))
                cur_start, cur_end = s, e
        merged.append((cur_start, cur_end))

        if len(merged) == 1:
            return 1

        # Gaps
        gaps = []
        for (s1, e1), (s2, e2) in zip(merged, merged[1:]):
            gaps.append((e1, s2))           # (end_of_first, start_of_second)

        # Sliding window
        l = 0
        max_covered = 0
        for r in range(len(gaps)):
            while l <= r and gaps[r][1] - gaps[l][0] > k:
                l += 1
            max_covered = max(max_covered, r - l + 1)

        return len(merged) - max_covered
```

---

## 8. Implementation – C++  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minConnectedGroups(vector<vector<int>>& intervals, int k) {
        sort(intervals.begin(), intervals.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 if (a[0] == b[0]) return a[1] > b[1];
                 return a[0] < b[0];
             });

        // Merge intervals
        vector<pair<long long, long long>> merged;
        long long curStart = intervals[0][0];
        long long curEnd   = intervals[0][1];
        for (size_t i = 1; i < intervals.size(); ++i) {
            long long s = intervals[i][0];
            long long e = intervals[i][1];
            if (s <= curEnd) {           // overlap
                curEnd = max(curEnd, e);
            } else {
                merged.emplace_back(curStart, curEnd);
                curStart = s;
                curEnd   = e;
            }
        }
        merged.emplace_back(curStart, curEnd);

        if (merged.size() == 1) return 1;

        // Gaps between merged intervals
        vector<pair<long long, long long>> gaps;
        for (size_t i = 0; i + 1 < merged.size(); ++i) {
            long long end1 = merged[i].second;
            long long start2 = merged[i + 1].first;
            gaps.emplace_back(end1, start2);   // (end, start)
        }

        // Sliding window over gaps
        size_t l = 0, maxCovered = 0;
        for (size_t r = 0; r < gaps.size(); ++r) {
            while (l <= r && gaps[r].second - gaps[l].first > k) {
                ++l;
            }
            maxCovered = max(maxCovered, r - l + 1);
        }

        return static_cast<int>(merged.size() - maxCovered);
    }
};
```

---

## 9. Edge Cases Handled  

| Edge | How We Handle It |
|------|-----------------|
| No intervals | Return 0 (unlikely due to constraints) |
| All intervals already overlapping → one group | Return 1 immediately |
| New interval cannot cover any gap | `maxCovered = 0`, answer = `groups` |
| Gaps exactly of length `k` | Window includes them (≤ k) |

---

## 10. Testing – Quick Unit Test (Java)  

```java
public static void main(String[] args) {
    int[][] a = {{1, 3}, {3, 5}, {6, 7}, {8, 9}, {10, 11}};
    System.out.println(new Solution().minConnectedGroups(a, 4)); // expected 2
}
```

Run the same test on Python and C++ to confirm consistency.

---

## 11. Take‑Away for Your Interview

* **Always think about merging first** – Reduces the problem to a linear sequence of gaps.  
* **Treat gaps as the main data structure** – They are the only parts you can “bridge”.  
* **Use a sliding window** – The classic two‑pointer technique applies even when the window’s constraint depends on a computed difference (`gapEnd - gapStart`).  
* **Beware of integer overflow** – Gaps and required lengths can reach 10⁹, so use 64‑bit types (Java `long`, C++ `long long`, Python’s native integers).  

---

## 12. Further Reading & Related Problems  

* **“Maximum number of non‑overlapping intervals”** – classic sliding window  
* **“Merge Intervals”** – foundational problem in interval manipulation  
* **“Longest subarray with sum ≤ k”** – sliding window analog for sums  

---

## 13. Final Verdict  

With the merge‑gap‑sliding‑window strategy, the problem becomes a matter of **one linear pass** after sorting.  
This elegant solution not only passes the time limits but also gives you a clean, maintainable code base in Java, Python, and C++.

> **Happy coding!** 🎉

---

> **Keywords**: interval merging, gaps, sliding window, LeetCode, algorithm interview, time complexity O(n log n).