---
title: LeetCode 1851. Minimum Interval to Include Each Query - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1851 – Minimum Interval to Include Each Query  
### Full‑stack solutions (Java | Python | C++)

Below you’ll find three complete, production‑ready implementations of the most efficient sweep‑line + priority‑queue solution.  
All three share the same core idea:

1. **Sort** intervals by start time.  
2. **Sort** queries by value (keeping their original indices).  
3. For each query `q`  
   * push every interval whose start ≤ `q` into a **min‑heap** ordered by interval length.  
   * pop every interval whose end < `q` (it cannot cover the query).  
   * the heap’s top is the smallest valid interval – its length is the answer for that query.  
4. Restore the answers in the original query order.

The solution runs in  
**O((n + m) log(n + m))** time and **O(n + m)** space – fast enough for the limits (≤ 10⁵ intervals & queries).

---

### 1️⃣ Java

```java
import java.util.*;

/**
 * Leetcode 1851 – Minimum Interval to Include Each Query
 *
 * The algorithm uses a sweep line + min‑heap.
 *   • Intervals are processed in order of start time.
 *   • Queries are processed in ascending order.
 *   • A priority queue keeps only intervals that could still contain the current query.
 *   • The queue’s top always gives the smallest interval covering the query.
 */
public class Solution {
    public int[] minInterval(int[][] intervals, int[] queries) {
        // Sort intervals by start time
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

        // Pair each query with its original index
        int[][] qWithIdx = new int[queries.length][2];
        for (int i = 0; i < queries.length; i++) {
            qWithIdx[i][0] = queries[i]; // value
            qWithIdx[i][1] = i;          // original position
        }
        // Sort queries by value
        Arrays.sort(qWithIdx, Comparator.comparingInt(a -> a[0]));

        // Min‑heap ordered by interval length (end - start + 1)
        PriorityQueue<int[]> heap =
                new PriorityQueue<>(Comparator.comparingInt(a -> a[0] - a[1] + 1));

        int[] result = new int[queries.length];
        int intervalIdx = 0; // pointer in intervals

        for (int[] q : qWithIdx) {
            int value = q[0];
            int originalIdx = q[1];

            // Add all intervals that start <= current query
            while (intervalIdx < intervals.length && intervals[intervalIdx][0] <= value) {
                heap.offer(intervals[intervalIdx++]);   // store the whole interval
            }

            // Remove intervals that ended before the query
            while (!heap.isEmpty() && heap.peek()[1] < value) {
                heap.poll();
            }

            // The top of the heap is the shortest interval covering the query
            result[originalIdx] = heap.isEmpty()
                    ? -1
                    : heap.peek()[1] - heap.peek()[0] + 1;
        }

        return result;
    }
}
```

---

### 2️⃣ Python

```python
import heapq
from typing import List

class Solution:
    def minInterval(self, intervals: List[List[int]], queries: List[int]) -> List[int]:
        """
        Sweep line + priority queue.

        Time  : O((n + m) log(n + m))
        Space : O(n + m)
        """
        # 1. Sort intervals by start time
        intervals.sort(key=lambda x: x[0])

        # 2. Attach original indices to queries
        qs = sorted([(q, i) for i, q in enumerate(queries)])

        # 3. Min‑heap holds (length, end)
        heap = []

        ans = [0] * len(queries)
        i = 0  # pointer in intervals

        for q, idx in qs:
            # add all intervals that start <= q
            while i < len(intervals) and intervals[i][0] <= q:
                start, end = intervals[i]
                heapq.heappush(heap, (end - start + 1, end))
                i += 1

            # remove intervals that ended before q
            while heap and heap[0][1] < q:
                heapq.heappop(heap)

            # answer for this query
            ans[idx] = heap[0][0] if heap else -1

        return ans
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

/*
 * Leetcode 1851 – Minimum Interval to Include Each Query
 * Sweep line with priority_queue (min‑heap).
 */
class Solution {
public:
    vector<int> minInterval(vector<vector<int>>& intervals,
                            vector<int>& queries) {
        // 1. Sort intervals by start
        sort(intervals.begin(), intervals.end(),
             [](const auto& a, const auto& b) { return a[0] < b[0]; });

        // 2. Keep original indices of queries
        vector<pair<int,int>> q(queries.size());
        for (size_t i = 0; i < queries.size(); ++i) {
            q[i] = {queries[i], static_cast<int>(i)};
        }
        sort(q.begin(), q.end(),
             [](const auto& a, const auto& b) { return a.first < b.first; });

        // 3. Min‑heap ordered by interval length
        using pii = pair<int,int>;               // {length, end}
        priority_queue<pii, vector<pii>, greater<pii>> pq;

        vector<int> ans(queries.size());
        size_t i = 0; // pointer in intervals

        for (auto [val, idx] : q) {
            // push intervals that start <= current query
            while (i < intervals.size() && intervals[i][0] <= val) {
                int len = intervals[i][1] - intervals[i][0] + 1;
                pq.emplace(len, intervals[i][1]);
                ++i;
            }
            // pop intervals that end before the query
            while (!pq.empty() && pq.top().second < val)
                pq.pop();

            ans[idx] = pq.empty() ? -1 : pq.top().first;
        }
        return ans;
    }
};
```

All three implementations use the same sweep‑line + priority‑queue strategy and run comfortably under the problem constraints.

---

# 📚 SEO‑Optimized Blog Post  
**Title:** *“Mastering Leetcode 1851 – Minimum Interval to Include Each Query: Sweep‑Line + Heap in Java, Python, C++”*  

**Meta Description:**  
Solve Leetcode 1851 in milliseconds. Learn the sweep‑line + priority queue strategy, full Java, Python, C++ codes, and why it outperforms brute‑force. Perfect for coding interviews, algorithm practice, and interview preparation.

---

## 1️⃣ Why This Problem Matters  
When you’re prepping for a coding interview, *interval queries* pop up all the time: “Find the smallest segment that contains this point.”  
Leetcode 1851 (Minimum Interval to Include Each Query) is a canonical example that tests:

- **Sorting skills** – you must order intervals and queries efficiently.  
- **Greedy + Data‑structure synergy** – the shortest covering interval is not obvious without a heap.  
- **Time‑space trade‑offs** – brute‑force TLE; optimal solution must be sub‑quadratic.

If you nail this, you’ll ace similar problems on real interviews and boost your algorithmic confidence.

---

## 2️⃣ Good – The Elegant Sweep‑Line + Heap Approach  
**Why it’s good:**  

| Aspect | What It Gives You | Why It Matters |
|--------|-------------------|----------------|
| **O((n+m) log(n+m))** | Fast for 10⁵ intervals/queries | Interview‑ready performance |
| **Only one pass** | No nested loops | Memory & CPU friendly |
| **Clear logic** | “Add when start ≤ q, pop when end < q” | Easy to explain & debug |
| **Reusable pattern** | Works for other interval‑query problems | Builds a coding toolkit |

### Key Insight  

At a query point `q` you only need intervals that:

- start **≤ q** (otherwise they can’t cover it)  
- end **≥ q** (otherwise they’ve expired)

The *smallest* such interval is what the min‑heap gives you instantly.

---

## 3️⃣ Bad – Brute‑Force, Hash Maps, or Two‑Pointer Missteps  
**Common pitfalls** that lead to “bad” solutions:

- **Hash map of starts → lengths** – O(n+m) to build but still requires scanning *all* intervals for each query → **O(n · m)**.  
- **Two‑pointer with a vector** – without a heap you can’t guarantee the shortest interval.  
- **Binary search on a sorted list of lengths** – you’d need to know whether each length can still cover `q`; that’s the same as the heap, but re‑implementing it manually is error‑prone.

> **Bottom line:**  
> Brute‑force (`for q in queries: for interval in intervals`) takes ~10⁹ operations → TLE.  
> Any solution that does **more than linear** work is usually suspect for this problem.

---

## 4️⃣ Ugly – Why Brute‑Force Fails  
If you’re new to the problem you might try a nested loop or a simple map of interval lengths.  
**What goes wrong?**

1. **Quadratic time** – 10⁵ × 10⁵ = 10¹⁰ operations.  
2. **Cache misses** – Random memory access in a large vector.  
3. **Hard to trace** – Even a small bug (off‑by‑one in length) can make the whole solution wrong.

Interviewers love you for explaining *why* a naive solution is unacceptable. Show them you know the pitfalls and why the heap wins.

---

## 5️⃣ The Implementation (Pseudo‑Code)

```
sort intervals by start
pair queries with original indices
sort queries by value

heap = min‑heap ordered by interval length

intervalPtr = 0
for value, origIdx in sorted queries:
    while intervalPtr < intervals.size and intervals[intervalPtr].start <= value:
        push intervals[intervalPtr] into heap
        intervalPtr++

    while heap not empty and heap.top.end < value:
        pop heap

    answer[origIdx] = heap empty ? -1 : heap.top.length
```

> This pseudocode is language‑agnostic; the three code snippets above are just a drop‑in replacement for the language you’re comfortable with.

---

## 6️⃣ Complexity Breakdown  
| Operation | Complexity | Explanation |
|-----------|------------|-------------|
| Sorting intervals | **O(n log n)** | Single pass sorting |
| Sorting queries | **O(m log m)** | Needed to process in order |
| Heap operations (push/pop) | **O((n+m) log n)** | Each interval and query causes at most one push or pop |

Total: **O((n+m) log(n+m))** time, **O(n + m)** memory.

---

## 7️⃣ Common Interview Questions Around This Problem  

1. **“Can you explain the greedy choice?”**  
   *Answer:* “At any query `q` we consider only intervals that might still cover it. The min‑heap guarantees we pick the one with the smallest length immediately.”

2. **“What if two intervals have the same length?”**  
   *Answer:* “The heap is stable for equal lengths because we also store the end point – it doesn’t affect correctness, just the tie‑breaking order.”

3. **“How would you adapt this if queries came in arbitrary order (not sorted)?”**  
   *Answer:* “You still sort them internally, but remember to store the original index so you can re‑assemble the output at the end.”

---

## 8️⃣ Final Thoughts & Interview Tips  

- **Show the pattern** – sweep‑line + heap is a powerful combo.  
- **Edge cases** – test with queries outside all intervals and intervals of length 1.  
- **Explain the heap** – many interviewers ask “Why a heap?” – say “Because we need the *minimum* length at all times.”  
- **Practice** – solve similar problems: *Meeting Rooms II*, *Car Fleet*, *Interval Cover*, *Maximum Overlap*, etc.

Mastering Leetcode 1851 gives you a reusable algorithmic building block that you can confidently bring into any data‑structure interview. Good luck! 🚀