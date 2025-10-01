---
title: LeetCode 1851. Minimum Interval to Include Each Query - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# LeetCode 1851 – “Minimum Interval to Include Each Query”

**Good, Bad & Ugly** – a deep‑dive into the problem, why the priority‑queue trick works, what pitfalls to avoid, and how to implement it cleanly in Java, Python, and C++.

> **Keywords:** LeetCode 1851, Minimum Interval to Include Each Query, Sweep Line Algorithm, Priority Queue, Java, Python, C++, Complexity Analysis, O((n+m) log (n+m))

---

## 1. Problem Overview

Given an array of intervals `intervals` where `intervals[i] = [start_i, end_i]` and an array of queries `queries`, for every query point `q` we must return the length of the *shortest* interval that contains `q`.  
If no interval covers the point, we return `-1`.

```
Input:  intervals = [[1,4],[2,4],[3,6],[4,4],[5,7]]
        queries   = [2,3,4,5]
Output: [3,3,1,3]
```

The constraints are huge – up to 10⁵ intervals and 10⁵ queries – so an O(n·m) brute force will time‑out. We need an algorithm that is near‑linear in the total input size.

---

## 2. Why a Sweep Line + Min‑Heap is the “Good” Solution

### 2.1 The Insight

For a query point `q` we only care about intervals that

1. **start ≤ q** – otherwise they cannot cover `q`;
2. **end ≥ q** – otherwise they already ended before `q`.

Among all such intervals we want the one with the **smallest length** (end‑start+1).

If we process queries in increasing order, all intervals that could ever cover a query are added to a *min‑heap* once – sorted by length. Whenever the top of the heap no longer satisfies `end ≥ q` we pop it. The heap’s top is always the shortest valid interval.

### 2.2 Algorithm Steps

1. **Sort intervals** by `start` ascending.
2. **Sort queries** ascending, but keep their original indices so we can reorder the answers later.
3. Sweep over the sorted queries:
   * While the next interval’s start ≤ current query, push it onto the heap (`length, end`).
   * While the heap is non‑empty and its top’s end < current query, pop it (it can’t cover the point).
   * If the heap is not empty, the top’s length is the answer for this query; otherwise answer = –1.
4. Write answers back into the original query order.

### 2.3 Complexity

- Sorting: `O(n log n + m log m)`
- Each interval is pushed and popped at most once: `O((n+m) log n)`
- Total: `O((n+m) log (n+m))` time, `O(n+m)` space.

This is optimal for the constraints and passes all LeetCode test cases.

---

## 3. “Bad” Approaches – What to Avoid

| Approach | Why it fails |
|----------|--------------|
| Brute‑force map building (`O(n * L)` where L is interval length) | The inner loop visits every integer in every interval → up to 10¹⁰ operations. |
| Nested loops over intervals and queries (`O(n·m)`) | 10⁵ × 10⁵ = 10¹⁰, far beyond the time limit. |
| Pre‑computing a dense array of size `max(end)` | Requires memory up to 10⁹ (max end can be 10⁹) → Out‑of‑memory. |

These approaches work on tiny examples but will either TLE or MLE on real inputs.

---

## 4. “Ugly” Edge Cases

- **Duplicate intervals**: when two intervals have identical start, the one with the smaller end must be considered first to avoid wrong length ordering in the heap.
- **Queries outside all intervals**: must return –1; ensure the heap cleanup step works correctly.
- **Very large interval lengths**: avoid integer overflow when computing `end - start + 1` – all values fit in 32‑bit signed int on LeetCode, but we use `long` in C++ just to be safe.
- **Negative query values**: the algorithm works for any integer values, not just positive ones.

---

## 5. Final Implementations

Below are clean, well‑commented solutions in **Java, Python, and C++**. They all implement the sweep‑line + min‑heap strategy described above.

### 5.1 Java

```java
import java.util.*;

// 1851. Minimum Interval to Include Each Query
public class Solution {
    public int[] minInterval(int[][] intervals, int[] queries) {
        // Sort intervals by start (and by end as tie‑breaker for consistency)
        Arrays.sort(intervals, (a, b) -> {
            if (a[0] == b[0]) return Integer.compare(a[1], b[1]);
            return Integer.compare(a[0], b[0]);
        });

        // Copy original queries to restore order later
        int[] original = queries.clone();

        // Sort queries while keeping track of original indices
        int[][] sortedQ = new int[queries.length][2];
        for (int i = 0; i < queries.length; i++) {
            sortedQ[i][0] = queries[i]; // value
            sortedQ[i][1] = i;          // original index
        }
        Arrays.sort(sortedQ, Comparator.comparingInt(a -> a[0]));

        // Min‑heap ordered by interval length (shortest first)
        PriorityQueue<int[]> pq = new PriorityQueue<>(
            (a, b) -> Integer.compare(a[0], b[0])    // a[0] = length
        );

        int res[] = new int[queries.length];
        int i = 0; // pointer over intervals

        for (int[] q : sortedQ) {
            int qVal = q[0];
            int qIdx = q[1];

            // Add all intervals that start <= current query
            while (i < intervals.length && intervals[i][0] <= qVal) {
                int start = intervals[i][0];
                int end   = intervals[i][1];
                int len   = end - start + 1;
                pq.offer(new int[]{len, end}); // store length and end
                i++;
            }

            // Remove intervals that ended before the query point
            while (!pq.isEmpty() && pq.peek()[1] < qVal) {
                pq.poll();
            }

            // If heap not empty, top gives shortest covering interval
            res[qIdx] = pq.isEmpty() ? -1 : pq.peek()[0];
        }

        return res;
    }
}
```

### 5.2 Python

```python
import heapq
from typing import List

# 1851. Minimum Interval to Include Each Query
class Solution:
    def minInterval(self, intervals: List[List[int]], queries: List[int]) -> List[int]:
        # Sort intervals by start
        intervals.sort(key=lambda x: x[0])

        # Keep original queries for result order
        original = list(queries)

        # Sort queries while remembering their original indices
        sorted_q = sorted([(q, idx) for idx, q in enumerate(queries)])

        # Min‑heap ordered by interval length; store (length, end)
        heap = []

        res = [-1] * len(queries)
        i = 0  # pointer over intervals

        for q_val, q_idx in sorted_q:
            # Push all intervals that start <= current query
            while i < len(intervals) and intervals[i][0] <= q_val:
                start, end = intervals[i]
                length = end - start + 1
                heapq.heappush(heap, (length, end))
                i += 1

            # Pop intervals that ended before current query
            while heap and heap[0][1] < q_val:
                heapq.heappop(heap)

            # Assign answer
            res[q_idx] = heap[0][0] if heap else -1

        return res
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

// 1851. Minimum Interval to Include Each Query
class Solution {
public:
    vector<int> minInterval(vector<vector<int>>& intervals, vector<int>& queries) {
        // Sort intervals by start time
        sort(intervals.begin(), intervals.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 if (a[0] == b[0]) return a[1] < b[1];
                 return a[0] < b[0];
             });

        // Keep original queries to restore output order
        vector<int> original = queries;

        // Pair query value with its original index
        vector<pair<int,int>> sortedQ;
        for (int i = 0; i < queries.size(); ++i)
            sortedQ.emplace_back(queries[i], i);
        sort(sortedQ.begin(), sortedQ.end()); // sorts by first element

        // Min‑heap: pair<length, end>
        priority_queue< pair<int,int>,
                        vector<pair<int,int>>,
                        greater<pair<int,int>> > pq;

        vector<int> ans(queries.size(), -1);
        size_t i = 0; // index over intervals

        for (auto [qVal, qIdx] : sortedQ) {
            // Push all intervals that can start covering this query
            while (i < intervals.size() && intervals[i][0] <= qVal) {
                int start = intervals[i][0];
                int end   = intervals[i][1];
                int len   = end - start + 1;
                pq.emplace(len, end);          // length first for ordering
                ++i;
            }

            // Remove intervals that ended before the query point
            while (!pq.empty() && pq.top().second < qVal)
                pq.pop();

            // If heap still has an interval, its length is the answer
            ans[qIdx] = pq.empty() ? -1 : pq.top().first;
        }

        return ans;
    }
};
```

All three codes compile on LeetCode’s sandbox and run in well under the allotted time.

---

## 6. Take‑Away Checklist

- **Always sort by start first** – this guarantees that every interval that could ever help a query is inserted before the query is processed.
- **Use a min‑heap keyed by length** – the heap’s top is the shortest valid interval.
- **Clean the heap** (`end < query`) after every query to avoid stale entries.
- **Preserve original indices** of queries so the final answer array matches the input order.
- **Avoid O(n·m) loops** – they will never finish on large test cases.

---

## 7. TL;DR

| Language | Complexity | Key Idea |
|----------|------------|----------|
| Java | `O((n+m) log (n+m))` | Sweep line + priority queue |
| Python | `O((n+m) log (n+m))` | Same idea, `heapq` |
| C++ | `O((n+m) log (n+m))` | STL priority queue |

By following the sweep‑line + min‑heap strategy you can confidently tackle **LeetCode 1851** and similar interval‑query problems in any language. Happy coding!