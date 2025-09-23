---
title: LeetCode 436. Find Right Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Master LeetCode 436 – “Find Right Interval”  
### The Good, The Bad, and The Ugly  
> *A SEO‑friendly, interview‑ready blog post with complete Java, Python & C++ code.*

---

### TL;DR  
- **Problem**: For each interval `[start, end]` find the interval that starts **after or at** its `end` and has the *smallest* such start.  
- **Key idea**: Sort the intervals by `start`, then binary‑search for the first interval whose `start >= end`.  
- **Complexity**: `O(n log n)` time, `O(n)` space.  
- **Languages**: Java, Python, C++ (ready to copy‑paste into LeetCode).  

---

## 1️⃣ Problem Recap

> **436. Find Right Interval**  
> **Medium**  

> You’re given an array of intervals, `intervals[i] = [starti, endi]`, where each `starti` is unique.  
> The *right interval* for interval `i` is an interval `j` such that  
> `startj >= endi` **and** `startj` is minimized.  
> Return an array of the indices of the right interval for each interval; use `-1` if none exists.

---

## 2️⃣ The Good – Why Binary Search Works

| ✔️ | Reason |
|----|--------|
| **Uniqueness of starts** | Guarantees that no two intervals share the same `start`, so the sorted order is strict and we can safely binary‑search. |
| **Logarithmic lookup** | Once we sort the intervals by `start`, a `lower_bound` (or `bisect_left`) finds the first interval that satisfies the condition in `O(log n)`. |
| **Clean mapping** | Keep the original indices in a pair with each interval, so we can return the correct index after sorting. |
| **No extra data structures** | We only need an auxiliary array for the sorted pairs; no hash maps or priority queues required. |

---

## 3️⃣ The Bad – Common Pitfalls

| ⚠️ | Mistake | Fix |
|----|---------|-----|
| **Not sorting the starts** | If you skip sorting, binary‑search is meaningless. | Sort first. |
| **Using `>` instead of `>=`** | The problem requires `start >= end`. | Use `lower_bound` or `bisect_left` (which uses `>=`). |
| **Off‑by‑one errors** | Remember that `bisect_left` returns `len(list)` if nothing is found. | Check for `index == n` and set answer to `-1`. |
| **Storing only starts** | You lose the original index. | Store tuples `(start, original_index)`. |
| **Ignoring negative coordinates** | The range can be negative (`-10⁶`). | No special handling needed; binary‑search works on any integer range. |

---

## 4️⃣ The Ugly – Edge Cases & Performance Hiccups

| 😬 | Edge Case | Why it matters |
|----|-----------|----------------|
| **Single interval** | `[ [1,2] ]` → answer `[-1]` | Avoid index errors when `n==1`. |
| **Intervals with equal `end`** | Multiple intervals may end at the same value; each must find *the same* next start. | Works fine with binary‑search. |
| **Very large input (`n = 20 000`)** | Sorting and searching must remain efficient. | `O(n log n)` is acceptable; avoid `O(n²)` approaches. |
| **Negative and positive extremes** | `start = -10⁶`, `end = 10⁶` | Standard integer comparison handles this. |

---

## 5️⃣ Solutions in 3 Languages

### 5.1 Java (LeetCode Signature)

```java
import java.util.*;

class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        // Pair: [start, originalIndex]
        int[][] sorted = new int[n][2];
        for (int i = 0; i < n; i++) {
            sorted[i][0] = intervals[i][0];
            sorted[i][1] = i;
        }
        Arrays.sort(sorted, Comparator.comparingInt(a -> a[0]));

        int[] starts = new int[n];
        for (int i = 0; i < n; i++) starts[i] = sorted[i][0];

        int[] result = new int[n];
        for (int i = 0; i < n; i++) {
            int end = intervals[i][1];
            int idx = lowerBound(starts, end);      // custom binary search
            result[i] = (idx == n) ? -1 : sorted[idx][1];
        }
        return result;
    }

    // Find first position where value >= target
    private int lowerBound(int[] arr, int target) {
        int lo = 0, hi = arr.length;   // hi is exclusive
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (arr[mid] < target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

**Explanation**  
- Build a `sorted` array of `(start, originalIndex)` pairs.  
- Extract only the `starts` into a separate array for binary search.  
- For each interval, binary‑search its `end` in the sorted starts.  
- Map the found position back to the original index.

---

### 5.2 Python 3

```python
class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        # Build list of (start, original_index) and sort by start
        sorted_intervals = sorted([(s, i) for i, (s, _) in enumerate(intervals)])
        starts = [s for s, _ in sorted_intervals]
        result = []

        for s, e in intervals:
            # bisect_left finds first start >= e
            idx = bisect.bisect_left(starts, e)
            result.append(-1 if idx == len(sorted_intervals) else sorted_intervals[idx][1])

        return result
```

**Explanation**  
- Uses Python’s `bisect_left` from the standard library.  
- `sorted_intervals` keeps the original indices.  
- `starts` is a simple list of start points for fast binary search.

---

### 5.3 C++ (LeetCode Signature)

```cpp
class Solution {
public:
    vector<int> findRightInterval(vector<vector<int>>& intervals) {
        int n = intervals.size();
        // Pair: {start, original_index}
        vector<pair<int,int>> sorted(n);
        for (int i = 0; i < n; ++i) {
            sorted[i] = {intervals[i][0], i};
        }
        sort(sorted.begin(), sorted.end(),
             [](const auto& a, const auto& b){ return a.first < b.first; });

        vector<int> starts(n);
        for (int i = 0; i < n; ++i) starts[i] = sorted[i].first;

        vector<int> ans(n, -1);
        for (int i = 0; i < n; ++i) {
            int end = intervals[i][1];
            auto it = lower_bound(starts.begin(), starts.end(), end);
            if (it != starts.end()) {
                int pos = it - starts.begin();
                ans[i] = sorted[pos].second;
            }
        }
        return ans;
    }
};
```

**Explanation**  
- Uses `std::lower_bound` on the `starts` vector.  
- Stores the original indices in `sorted`.  
- Handles the `-1` case when the iterator reaches `end()`.

---

## 6️⃣ SEO & Job‑Search Value

| ✅ | Benefit |
|----|---------|
| **Keywords** | LeetCode 436, Find Right Interval, interview coding question, binary search, Java solution, Python solution, C++ solution, algorithm interview |
| **Job Readiness** | Shows mastery of sorting, binary search, and index mapping—core concepts every backend or systems engineer should know. |
| **Readable Code** | Clean, idiomatic, and ready for a code‑review or portfolio showcase. |
| **Blog Format** | Structured sections, markdown code blocks, and SEO‑friendly headings improve visibility on Google and LinkedIn. |

---

## 7️⃣ Take‑Away Checklist

- [ ] Sort intervals by `start`, keep original indices.  
- [ ] Binary‑search (`lower_bound` / `bisect_left`) for each interval’s `end`.  
- [ ] Map found position back to the original index.  
- [ ] Handle `-1` when no suitable interval exists.  
- [ ] Test with edge cases: single interval, negative numbers, duplicate ends.  

Happy coding, and may your next interview be a perfect match! 🚀  

--- 

> **Want to keep the code fresh?**  
> Subscribe to our newsletter for weekly LeetCode walkthroughs, interview hacks, and career‑boosting articles.  

--- 

*Feel free to drop a comment or share your own solutions. Let’s learn together!*