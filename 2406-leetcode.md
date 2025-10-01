---
title: LeetCode 2406. Divide Intervals Into Minimum Number of Groups - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 2406. Divide Intervals Into Minimum Number of Groups – Code + Blog

---

## Table of Contents

1. [Problem Overview](#problem-overview)
2. [Intuition & Core Idea](#intuition--core-idea)
3. [Detailed Algorithm](#detailed-algorithm)
4. [Complexity Analysis](#complexity-analysis)
5. [Edge Cases & Pitfalls](#edge-cases--pitfalls)
6. [Code Implementations](#code-implementations)
   - [Python 3](#python-3)
   - [Java](#java)
   - [C++ (Modern)](#c-pp-modern)
7. [The Good, The Bad, and The Ugly](#the-good-the-bad-and-the-ugly)
8. [SEO‑Friendly Summary for Job Hunters](#seo-friendly-summary-for-job-hunters)
9. [References](#references)

---

## Problem Overview <a name="problem-overview"></a>

> **Divide Intervals Into Minimum Number of Groups**  
> Given `intervals[i] = [left_i, right_i]` (inclusive), partition them into the fewest groups such that no two intervals in the same group intersect.  
> Return the minimum number of groups required.

Intervals *intersect* if they share at least one integer point, e.g. `[1,5]` and `[5,8]` intersect.

Constraints  
- `1 ≤ intervals.length ≤ 10⁵`  
- `1 ≤ left_i ≤ right_i ≤ 10⁶`

---

## Intuition & Core Idea <a name="intuition--core-idea"></a>

The problem is identical to finding the **maximum number of overlapping intervals at any point** – that is the *minimum number of rooms* required to host all meetings (LeetCode 253 – Meeting Rooms II).  

The classic greedy solution:

1. **Sort** all start times and end times independently.  
2. Use two pointers: one iterating over starts, the other over the earliest ending interval.  
3. If a new interval starts after the earliest end (`start > end`), we can reuse that “room” – move the end pointer.  
4. Otherwise we need a new room – increment the group counter.  

The counter after processing all starts equals the minimal number of non‑overlapping groups.

---

## Detailed Algorithm <a name="detailed-algorithm"></a>

```text
Input:  intervals[] = [[l1,r1], [l2,r2], … , [ln,rn]]
Output: minimal number of groups

1. Extract two arrays:
   starts = [l1, l2, … , ln]
   ends   = [r1, r2, … , rn]

2. Sort both arrays (O(n log n)).

3. Initialize:
   end_ptr = 0          // points to earliest end
   groups  = 0          // current number of needed groups

4. For each start in starts:
     if start > ends[end_ptr]:
          // The earliest finishing interval has finished before this one starts
          // Reuse that group → move to next end
          end_ptr += 1
     else:
          // No interval has finished → need a new group
          groups += 1

5. Return groups
```

**Why it works**

- Sorting ensures that when we iterate over starts, we always know the *next* earliest end.
- If a start is strictly greater than the earliest end, the corresponding group is free.
- If not, all current groups are still occupied – we must spawn a new group.
- The algorithm effectively counts the maximum concurrent intervals.

---

## Complexity Analysis <a name="complexity-analysis"></a>

| Step                      | Time      | Space |
|---------------------------|-----------|-------|
| Extracting starts/ends   | `O(n)`    | `O(n)` |
| Sorting starts & ends    | `O(n log n)` | `O(n)` |
| Single scan of starts    | `O(n)`    | –     |
| **Total**                 | **`O(n log n)`** | **`O(n)`** |

This is optimal because we must at least sort the intervals (or process events), which costs `Ω(n log n)` in the worst case.

---

## Edge Cases & Pitfalls <a name="edge-cases--pitfalls"></a>

| Case | What to watch out for |
|------|------------------------|
| **Intervals touching at endpoints** | They *do* intersect. Use `start > end` to allow reuse only if strictly after. |
| **Large numbers** | Use `int` (32‑bit) – constraints fit comfortably. |
| **All intervals equal** | Each needs its own group. |
| **Single interval** | Return `1`. |
| **Many non‑overlapping intervals** | Return `1` (the algorithm will correctly reuse the group). |

Common mistakes:

- Using `>=` instead of `>` when comparing `start` and `end` – will over‑count groups.
- Forgetting to sort both arrays independently.
- Off‑by‑one errors when incrementing the `end_ptr` (make sure `end_ptr < n` before accessing `ends[end_ptr]` – but the algorithm guarantees this).

---

## Code Implementations <a name="code-implementations"></a>

### Python 3 <a name="python-3"></a>

```python
from typing import List

class Solution:
    def minGroups(self, intervals: List[List[int]]) -> int:
        """
        Returns the minimum number of groups such that
        no two intervals in the same group overlap.
        """
        if not intervals:
            return 0

        # Separate starts and ends
        starts = sorted(i[0] for i in intervals)
        ends   = sorted(i[1] for i in intervals)

        end_ptr, groups = 0, 0
        for s in starts:
            if s > ends[end_ptr]:
                # Reuse a group whose interval ended
                end_ptr += 1
            else:
                # Need a new group
                groups += 1

        return groups
```

**Why it’s clean**

- Uses generator expressions for concise extraction.
- Handles the empty input guard for safety.
- No extra data structures (just two lists).

---

### Java <a name="java"></a>

```java
import java.util.Arrays;

class Solution {
    public int minGroups(int[][] intervals) {
        if (intervals == null || intervals.length == 0) return 0;

        int n = intervals.length;
        int[] starts = new int[n];
        int[] ends   = new int[n];

        for (int i = 0; i < n; i++) {
            starts[i] = intervals[i][0];
            ends[i]   = intervals[i][1];
        }

        Arrays.sort(starts);
        Arrays.sort(ends);

        int endPtr = 0, groups = 0;

        for (int s : starts) {
            if (s > ends[endPtr]) {
                endPtr++;          // reuse a group
            } else {
                groups++;          // new group needed
            }
        }

        return groups;
    }
}
```

*Key points*

- Uses `Arrays.sort` – O(n log n) in Java.
- `int` suffices because `right_i ≤ 10⁶`.
- Clean separation of start/end arrays.

---

### C++ (Modern, C++17) <a name="c-pp-modern"></a>

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int minGroups(std::vector<std::vector<int>>& intervals) {
        if (intervals.empty()) return 0;
        int n = intervals.size();

        std::vector<int> starts(n), ends(n);
        for (int i = 0; i < n; ++i) {
            starts[i] = intervals[i][0];
            ends[i]   = intervals[i][1];
        }

        std::sort(starts.begin(), starts.end());
        std::sort(ends.begin(), ends.end());

        int endPtr = 0, groups = 0;
        for (int s : starts) {
            if (s > ends[endPtr]) {
                ++endPtr;     // interval finished – reuse
            } else {
                ++groups;     // need another group
            }
        }
        return groups;
    }
};
```

*Why modern C++?*

- Uses range‑based `for` loops.
- `std::vector` handles dynamic size.
- Sorting via `<algorithm>` – standard library.

---

## The Good, The Bad, and The Ugly <a name="the-good-the-bad-and-the-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic simplicity** | Two sorted arrays + two pointers = O(n log n) | None | Trying to build an interval tree or segment tree for the same task is over‑engineering |
| **Memory usage** | O(n) for two arrays | Avoids extra priority queue memory | Using `PriorityQueue` can be O(n) but with higher constant factors |
| **Readability** | Clear variable names (`starts`, `ends`, `endPtr`) | `start > end` can be confusing; add comment | Using `>=` instead of `>` causes subtle bugs |
| **Performance** | Linear scan after sorting – fast | Sorting is still required; no sub‑O(n log n) algorithm exists | Re‑sorting each query on the fly is disastrous |
| **Corner‑case handling** | Touching endpoints handled by strict > | None | Forgetting to increment `endPtr` can lead to out‑of‑bounds |

**Takeaway**: Keep the solution lean – sorting + greedy two‑pointer is the gold standard for interval grouping and meeting room problems.

---

## SEO‑Friendly Summary for Job Hunters <a name="seo-friendly-summary-for-job-hunters"></a>

> 🚀 **Boost your interview score with the *Divide Intervals Into Minimum Number of Groups* solution**  
> *Master a LeetCode Medium problem, showcase algorithmic thinking, and impress recruiters with clean O(n log n) code in Python, Java, and C++.*  

**Why this matters to hiring managers**

- **Algorithmic maturity**: Demonstrates knowledge of greedy strategies, sweep line, and two‑pointer techniques.  
- **Language versatility**: Provides production‑ready snippets in three major languages.  
- **Scalability**: Handles up to 100k intervals – real‑world big‑data sense.  
- **Best‑practice code**: Clear naming, comments, and edge‑case safety.  

📌 **Keywords**: LeetCode 2406, interval scheduling, greedy algorithm, two-pointer, meeting room problem, O(n log n), Python solution, Java solution, C++ solution, job interview, software engineering, algorithmic interview prep.

---

## References <a name="references"></a>

- [LeetCode 2406 – Divide Intervals Into Minimum Number of Groups](https://leetcode.com/problems/divide-intervals-into-minimum-number-of-groups/)  
- [LeetCode 253 – Meeting Rooms II (classic analogy)](https://leetcode.com/problems/meeting-rooms-ii/)  
- LeetCode discuss posts:  
  - https://leetcode.com/problems/divide-intervals-into-minimum-number-of-groups/solutions/5901109/  
  - https://leetcode.com/problems/divide-intervals-into-minimum-number-of-groups/solutions/2560101/  

Feel free to adapt the code snippets to your own style or platform! Happy coding and good luck on your next interview. 🚀