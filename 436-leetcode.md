---
title: LeetCode 436. Find Right Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Leetcode 436 – **Find Right Interval**  
*From easy‑to‑understand solutions to job‑ready interview code (Java / Python / C++).*

---

## TL;DR – The One‑Line Solution

```text
Right interval = first start ≥ interval.end  (sorted by start)
```

* Sort the intervals by their `start` values (keeping the original indices).  
* For each interval run a binary search on the sorted starts to locate the
  smallest start that is ≥ the current interval’s `end`.  
* If found, return that interval’s original index; otherwise return `-1`.

Complexity: **O(n log n)** time, **O(n)** extra space.

---

## Why This Problem Is a “Job‑Interview Gold Mine”

* **Core Concepts Tested**: sorting, binary search, hash maps, pointers.  
* **Languages**: Java, Python, C++ – the most common interview languages.  
* **Variants**: The same logic applies to “Find the Next Greater Element”,
  “Search in Rotated Sorted Array”, etc.  
* **Interview Talking‑Points**:  
  * Explain why sorting is needed.  
  * Discuss the use of `Map` / `unordered_map` to preserve original indices.  
  * Talk about edge‑cases (no right interval, negative values).  
  * Optimize for time vs. space.

If you can explain this solution cleanly in **under 5 minutes** you’ll win many interviewers’ favor.

---

## 1. Problem Recap (Leetcode 436)

Given an array `intervals` where each element is `[start, end]` and each `start`
is unique, for every interval find the index of the *right interval*:

* A right interval `j` satisfies `start[j] ≥ end[i]`.  
* Among all such intervals, pick the one with the **minimum** start value.  
* If none exists, return `-1`.

Return an array of right‑interval indices for each interval in the order
originally given.

**Constraints**

| Parameter | Size | Value Range |
|-----------|------|-------------|
| `intervals.length` | `1 … 2·10⁴` | |
| `intervals[i].length` | `2` | |
| `-10⁶ ≤ start ≤ end ≤ 10⁶` | | |
| `start` values are unique | | |

---

## 2. The Greedy + Binary Search Idea

1. **Keep Original Indices** – create a list of pairs `(start, originalIndex)`.  
2. **Sort by Start** – now we can binary‑search on `start`.  
3. **Binary Search** – for each interval’s `end`, find the *lower bound* in the
   sorted starts: the first start that is ≥ `end`.  
4. **Answer** – if such a start exists, its stored original index is the answer;
   otherwise `-1`.

Why it works:  
All starts are unique → the sorted order is strict, so the lower bound is
well‑defined. Because we sorted once, every lookup is `O(log n)`.

---

## 3. Implementation Highlights

| Language | Key Library |
|----------|--------------|
| **Java** | `Arrays.binarySearch`, `Map<Integer,Integer>`, `Arrays.sort` |
| **Python** | `bisect_left`, `sorted`, `dict` |
| **C++** | `lower_bound`, `vector<pair<int,int>>`, `unordered_map` |

All solutions are **iterative** (no recursion) and avoid expensive operations
inside the loop.

---

## 4. Code (Java, Python, C++)

### 4.1 Java – Clean, Interview‑Ready

```java
import java.util.*;

public class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[] answer = new int[n];

        // (start, originalIndex)
        int[][] startIdx = new int[n][2];
        for (int i = 0; i < n; i++) {
            startIdx[i][0] = intervals[i][0];
            startIdx[i][1] = i;
        }
        // sort by start value
        Arrays.sort(startIdx, Comparator.comparingInt(a -> a[0]));

        // extract sorted starts for binary search
        int[] starts = new int[n];
        for (int i = 0; i < n; i++) starts[i] = startIdx[i][0];

        for (int i = 0; i < n; i++) {
            int end = intervals[i][1];
            int pos = lowerBound(starts, end);   // custom lower bound
            answer[i] = (pos < n) ? startIdx[pos][1] : -1;
        }
        return answer;
    }

    // Standard binary search for lower bound
    private int lowerBound(int[] arr, int target) {
        int lo = 0, hi = arr.length;
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (arr[mid] < target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

> **Why not `Arrays.binarySearch`?**  
> `Arrays.binarySearch` returns an index only when the key is present; otherwise
> it returns a negative insertion point. `lowerBound` is clearer and avoids
> double negations.

---

### 4.2 Python – Elegant & Readable

```python
from bisect import bisect_left
from typing import List

class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        # (start, original_index) sorted by start
        starts = sorted((interval[0], idx) for idx, interval in enumerate(intervals))
        sorted_starts = [s for s, _ in starts]

        res = []
        for end in (interval[1] for interval in intervals):
            pos = bisect_left(sorted_starts, end)
            res.append(starts[pos][1] if pos < len(starts) else -1)
        return res
```

---

### 4.3 C++ – High‑Performance & Classic

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> findRightInterval(std::vector<std::vector<int>>& intervals) {
        int n = intervals.size();
        std::vector<std::pair<int, int>> startIdx(n); // (start, original idx)

        for (int i = 0; i < n; ++i) {
            startIdx[i] = {intervals[i][0], i};
        }

        std::sort(startIdx.begin(), startIdx.end(),
                  [](const auto& a, const auto& b){ return a.first < b.first; });

        std::vector<int> starts(n);
        for (int i = 0; i < n; ++i) starts[i] = startIdx[i].first;

        std::vector<int> ans(n);
        for (int i = 0; i < n; ++i) {
            int end = intervals[i][1];
            auto it = std::lower_bound(starts.begin(), starts.end(), end);
            ans[i] = (it != starts.end()) ? startIdx[it - starts.begin()].second : -1;
        }
        return ans;
    }
};
```

> **Why use `std::lower_bound`?**  
> It directly implements the binary search for the first element ≥ `target`
> – exactly what we need.

---

## 5. The Good, the Bad, the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | `O(n log n)` is optimal for unsorted data. | None – algorithm is already optimal. | None. |
| **Space Complexity** | `O(n)` for the sorted start array. | Extra overhead of storing indices, but unavoidable. | None. |
| **Code Simplicity** | Single pass + binary search. | Requires understanding of “lower bound”. | If you try to use heaps or two‑pointer trick, it gets messy. |
| **Edge Cases** | Handles negative starts, large ranges. | Need to remember to use 64‑bit if range > `int`. | Forgetting that `start` values are unique can break logic. |
| **Language‑Specific Pitfalls** | Java: `Arrays.binarySearch` negative values. | Python: `bisect_left` vs `bisect_right`. | C++: iterator arithmetic off‑by‑one. |

**Bottom Line:** Stick to the **sorted + binary search** pattern. It’s clean, fast, and covers all edge cases.

---

## 6. SEO‑Optimized Blog Article

### Title  
**Leetcode 436 – Find Right Interval: Java / Python / C++ Solutions & Interview Guide**

### Meta Description  
Master Leetcode 436 “Find Right Interval” with step‑by‑step solutions in Java, Python, and C++. Learn the optimal algorithm, trade‑offs, and interview‑ready explanations to boost your coding interview prep.

---

### 1️⃣ What is “Find Right Interval”?

Leetcode’s **436** asks you to find, for each interval, the smallest interval whose start is at least the current interval’s end. It’s a classic “search the next greater value” problem but with intervals.

---

### 2️⃣ Why Should You Care?

* **Interview gold:** Sorting + binary search shows you know data‑structure fundamentals.  
* **Job‑ready:** Companies like Google, Amazon, and Stripe ask variations of this.  
* **Cross‑language:** The logic is identical in Java, Python, and C++ – perfect for multilingual portfolios.

---

### 3️⃣ The Optimal Solution in a Nutshell

1. **Keep indices** – store `(start, originalIndex)` pairs.  
2. **Sort by start** – one O(n log n) pass.  
3. **Binary search** – for each `end`, find the lower bound in the sorted starts.  
4. **Return index** – if found, use stored original index; otherwise `-1`.

---

### 4️⃣ Step‑by‑Step Walkthrough (Python)

```python
from bisect import bisect_left

def findRightInterval(intervals):
    starts = sorted((s, i) for i, (s, _) in enumerate(intervals))
    sorted_starts = [s for s, _ in starts]
    result = []

    for _, e in intervals:
        pos = bisect_left(sorted_starts, e)
        result.append(starts[pos][1] if pos < len(starts) else -1)
    return result
```

*Time*: `O(n log n)`  
*Space*: `O(n)`

---

### 5️⃣ C++ & Java Code (Same Idea)

* **C++** – uses `std::lower_bound`.  
* **Java** – uses custom `lowerBound` to avoid confusion with `Arrays.binarySearch`.

Both are under 40 lines, fully compilable, and ready for Leetcode.

---

### 6️⃣ Interview Talking Points

1. **Why sorting?**  
   *We need to quickly find the next start ≥ end. Sorting turns the problem into a binary search.*

2. **Why binary search?**  
   *We’re looking for the *smallest* qualifying start; lower bound gives it in `O(log n)`.*

3. **Space trade‑off**  
   *We store an auxiliary array of starts + indices. Still linear space, but far cheaper than a heap solution.*

4. **Edge cases**  
   *No right interval → return `-1`.  
   *Negative start values & large ranges → use `int` (range ±10⁶ fits comfortably).*

5. **Alternative solutions**  
   *TreeMap (Java) / std::map (C++) – O(n log n) but constant factor higher.  
   *PriorityQueue approach – O(n²) worst case.*

---

### 7️⃣ TL;DR – Quick Reference

| Language | Key Functions | Complexity |
|----------|---------------|------------|
| **Java** | `Arrays.sort`, custom `lowerBound` | `O(n log n)` |
| **Python** | `bisect_left`, `sorted` | `O(n log n)` |
| **C++** | `std::sort`, `std::lower_bound` | `O(n log n)` |

---

### 8️⃣ Takeaway

The “Find Right Interval” problem is a perfect showcase of *sorting + binary search*.
Mastering this solution demonstrates to recruiters that you can:

* Reduce a seemingly complex search to a standard algorithmic pattern.  
* Write clean, cross‑language code.  
* Talk confidently about time/space trade‑offs.

Happy coding, and good luck with your next interview! 🚀

---

## 9️⃣ Bonus Resources

| Topic | Resource |
|-------|----------|
| Binary Search fundamentals | [GeeksforGeeks – Binary Search](https://www.geeksforgeeks.org/binary-search/) |
| Interview prep courses | LeetCode Explore, Cracking the Coding Interview |
| Portfolio showcase | GitHub repos with language tags, Leetcode submission stats |

---

## 9️⃣ Final Thoughts

Now that you have:

* **Three complete solutions** (Java, Python, C++).  
* A **clear algorithmic rationale**.  
* A **ready‑to‑publish blog article** to showcase in your résumé.

You’re ready to turn Leetcode 436 into a portfolio win and secure that coveted coding interview call. Keep practicing and explore variations – the next challenge is just around the corner! 🌟

--- 

*© 2024 Your Coding Journey.* 

--- 

### Word Count  
≈ 900 words (ready for SEO, concise, and easy to skim).